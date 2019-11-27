---
title: Csoportmunka-fejlesztés a .NET Core és a Visual Studio használatával
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 895d2edbb268eab9944909ecda7193ce945bbf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325537"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Csoportmunka-fejlesztés a .NET Core és a Visual Studióval az Azure dev Spaces használatával

Ebből az oktatóanyagból megtudhatja, hogy a fejlesztők hogyan dolgozhatnak egyszerre ugyanazon a Kubernetes-fürtön a dev Spaces használatával.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

Eddig úgy futtatta alkalmazása kódját, mintha Ön dolgozna egyedül fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Lehetővé teheti a fejlesztők számára, hogy ugyanabban a környezetben működjenek, ha a megosztott fejlesztői térben vagy a különböző fejlesztői területeken szükség szerint dolgozik.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A minta alkalmazása jelenleg nem összetett. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat.

* Előfordulhat, hogy a fejlesztői gép nem rendelkezik elegendő erőforrással az összes szükséges szolgáltatás futtatásához.
* Előfordulhat, hogy egyes szolgáltatásoknak nyilvánosan elérhetőnek kell lenniük. Előfordulhat például, hogy egy szolgáltatásnak rendelkeznie kell egy webhookra válaszoló végponttal.
* Ha a szolgáltatások egy részhalmazát szeretné futtatni, ismernie kell a teljes függőségi hierarchiát az összes szolgáltatás között. A hierarchia meghatározása nehéz lehet, különösen, ha a szolgáltatások száma növekszik.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a megközelítés segíthet, de az ilyen modellek kezelése hamarosan hatással lehet a fejlesztési díjakra. Emellett ez a megközelítés az éles környezettől eltérő fejlesztési környezetet eredményez, amely finom hibákhoz vezethet.
* Ebből következik, hogy az integrációs tesztek bármilyen típusát megnehezíti. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Fejlesztői területek használata a csapatmunkához
Bemutatjuk ezeket az ötleteket egy konkrét példával, amely a *webfrontend* -> *mywebapi* minta alkalmazását használja. Képzeljük el, hogy egy fejlesztő, Scott, a *mywebapi* szolgáltatást kell módosítania, és *csak* ezt a szolgáltatást kell használnia. A *webfrontend* nem kell változtatnia a Scott frissítésének részeként.

A dev Spaces használata _nélkül_ Scott számos módon fejlesztheti és tesztelheti a frissítését, amelyek közül egyik ideális megoldás:
* Az összes összetevőt helyileg futtassa, amelyhez a Docker által telepített és potenciálisan MiniKube nagyobb teljesítményű fejlesztői gépek szükségesek.
* Futtassa az összes összetevőt egy elkülönített névtérben a Kubernetes-fürtön. Mivel a *webfrontend* nem változik, az elkülönített névtér használata a fürterőforrások hulladéka.
* CSAK futtasson *mywebapi*, és VÉGEZZEN manuális Rest-hívásokat a teszteléshez. Ez a típusú tesztelés nem ellenőrzi a teljes végpontok közötti folyamatot.
* Bővítse a fejlesztéssel célzott kódot a *webfrontendhez* , amely lehetővé teszi, hogy a fejlesztő kéréseket küldjön a *mywebapi*egy másik példányára. A kód hozzáadása megnehezíti a *webfrontend* szolgáltatást.

### <a name="set-up-your-baseline"></a>Az Alapterv beállítása
Először üzembe kell helyeznie a szolgáltatásaink alapkonfigurációját. Ez a központi telepítés a "legutóbbi ismert jó" állapotot fogja tartalmazni, így egyszerűen összehasonlíthatja a helyi kód viselkedését és a bejelentkezett verziót. Ezután létrehozunk egy gyermek területet ezen alapkonfiguráció alapján, hogy a nagyobb alkalmazás kontextusában teszteljük a *mywebapi* módosításait.

1. A [dev Spaces minta alkalmazás](https://github.com/Azure/dev-spaces)klónozása: `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Tekintse meg a távoli ág *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Zárjon be minden F5/hibakeresési munkamenetet minden szolgáltatás esetében, de tartsa nyitva a projekteket saját Visual Studio-ablakukban.
1. Váltson a Visual Studio ablakába a _mywebapi_ projekttel.
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
1. A **módosítás** gombra kattintva hozza létre az F5 vagy a CTRL + F5 billentyűkombinációt a szolgáltatáshoz használni kívánt terület létrehozásához.
1. A hely legördülő menüben válassza a **\<új terület létrehozása...\>** lehetőséget.
1. Győződjön meg arról, hogy a szülő terület **\<nincs\>** , és adja meg a Space name **dev**értéket. Kattintson az OK gombra.
1. Nyomja le a CTRL + F5 billentyűkombinációt a _mywebapi_ futtatásához a hibakereső csatolása nélkül.
1. Váltson a Visual Studio ablakába a _webfrontend_ -projekttel, és nyomja le a CTRL + F5 billentyűkombinációt a futtatásához is.

> [!Note]
> Néha frissíteni kell a böngészőlapot azt követően, hogy a weboldal először megjelenik a Ctrl+F5 lenyomása után.

> [!TIP]
> A fenti lépések manuálisan állítanak be egy alapkonfigurációt, de azt javasoljuk, hogy a Teams a CI/CD használatával automatikusan tartsa naprakészen az alapkonfigurációt a véglegesített kóddal.
>
> Az alábbi ábrához hasonló munkafolyamat létrehozásához tekintse meg a [CI/CD beállítása az Azure DevOps című útmutatót](how-to/setup-cicd.md) .
>
> ![Példa CI/CD diagramra](media/common/ci-cd-complex.png)

Bárki, aki megnyitja a nyilvános URL-címet, és a webalkalmazásra navigál, meghívja a kód elérési útját, amely az alapértelmezett _fejlesztői_ helyet használó szolgáltatásokon keresztül fut. Most tegyük fel, hogy folytatni kívánja a *mywebapi* fejlesztését – hogyan teheti ezt meg, és nem szakítja meg a fejlesztői területet használó többi fejlesztőt? Ehhez be kell állítania a saját terét.

### <a name="create-a-new-dev-space"></a>Új Dev Spaces-tér létrehozása
A Visual Studióból létrehozhat további tereket, amelyek akkor lesznek használatban, ha a szolgáltatásában lenyomja az F5, illetve a Ctrl+F5 billentyűt. Olyan nevet adhat a tereknek, amilyet csak szeretne, és rugalmasan kezelheti a jelentését (pl.: _sprint4_ vagy _bemutató_).

Új teret a következő módon hozhat létre:
1. Váltson a Visual Studio ablakába a *mywebapi* projekttel.
2. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
3. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
4. Itt módosíthatja vagy létrehozhatja azt a fürtöt és/vagy teret, amelyet a program akkor használ, ha lenyomja az F5, illetve a Ctrl+F5 billentyűt. *Győződjön meg róla, hogy a korábban létrehozott Azure Dev Spaces-tér van kiválasztva*.
5. A hely legördülő menüben válassza a **\<új terület létrehozása...\>** lehetőséget.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. A **terület hozzáadása** párbeszédpanelen állítsa a szülő területet a **dev**értékre, és adja meg az új terület nevét. Használhatja a saját nevét (pl.: „scott”) az új térhez, így munkatársai számára is könnyen beazonosítható a munkájához használt tér. Kattintson az **OK** gombra.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Most már láthatja az AKS-fürtöt és az új, a projekttulajdonságok oldalán kiválasztott teret.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Kód frissítése a *mywebapi*-hoz

1. A *mywebapi* projektben a következőképpen módosítható a `string Get(int id)` metódus a fájlban `Controllers/ValuesController.cs` a következő módon:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Állítson be egy töréspontot a frissített kódrészletben (elképzelhető, hogy korábban már megadott egyet).
3. A _mywebapi_ szolgáltatás elindításához nyomja le az F5 billentyűt, amely a kijelölt terület használatával elindítja a szolgáltatást a fürtben. Ebben az esetben a kiválasztott terület _Scott_.

Ez az ábra segít megérteni, hogyan működnek a különféle terek. A lila elérési útja a _fejlesztői_ területtől érkező kérést jeleníti meg, amely az alapértelmezett elérési út, amelyet az URL-cím nem előtagértéke. A rózsaszín elérési út a _dev/Scott_ Space használatával jeleníti meg a kérést.

![](media/common/Space-Routing.png)

Az Azure Dev Spaces e beépített funkciója lehetővé teszi, hogy végpontok között tesztelhesse a kódot egy megosztott környezetben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját terük teljes szolgáltatási vermét. Ez az útválasztás megköveteli, hogy a propagálási fejlécek továbbítva legyenek az alkalmazáskódban, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-running-in-the-_devscott_-space"></a>A _fejlesztői/Scott_ -térben futó kód tesztelése
A *mywebapi* új verziójának a *webfrontendtel*való együttes teszteléséhez nyissa meg a böngészőt a *webfrontend* nyilvános hozzáférési pontjának URL-címére (például http://dev.webfrontend.123456abcdef.eus.azds.io), és lépjen a névjegy lapra. Az eredeti, „Hello from webfrontend and Hello from mywebapi” üzenetnek kell megjelennie.

Most adja hozzá a „scott.s” kifejezést az URL-cím részeként olvasson be valamit, például http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io, és frissítse a böngészőt. A *mywebapi* -projektben megadott töréspontnak találatot kell kapnia. Nyomja le az F5-öt a folytatáshoz. A böngészőben megjelenik az új üzenet: „Hello from webfrontend and mywebapi now says something new.” Ennek az az oka, hogy a frissített kód elérési útja a *mywebapi* -ben a _dev/Scott_ -térben fut.

Ha olyan _fejlesztői_ területtel rendelkezik, amely mindig tartalmazza a legújabb módosításokat, és feltételezi, hogy az alkalmazás úgy van kialakítva, hogy kihasználja a DevSpace-alapú útválasztást az oktatóanyag szakaszban leírtak szerint, remélhetőleg könnyen megtekintheti, hogy a fejlesztői helyek hogyan segíthetnek a nagyobb alkalmazások környezetében rejlő új szolgáltatások tesztelésében. Ahelyett, hogy az _összes_ szolgáltatást a saját tárhelyére kellene telepítenie, létrehozhat egy olyan privát helyet, amely a _dev_-ből származik, és csak a ténylegesen használt szolgáltatások közül választhat. A dev Spaces útválasztási infrastruktúrája a REST-t úgy fogja kezelni, hogy a saját tárterületének számos szolgáltatását használja, miközben a keresés közben a legújabb verzióra, a _fejlesztői_ térben pedig az alapértelmezett értékre kerül. És még jobb, ha _több_ fejlesztő is aktívan fejleszti a különböző szolgáltatásokat a saját területén anélkül, hogy megzavarja egymást.

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * A fejlesztői Spaces használatával egyszerűen tesztelheti az elkülönített módosításokat egy nagyobb, nagy teljesítményű szolgáltatásalkalmazás környezetében.

Most, hogy megismerte az Azure Dev Spacest, [megoszthatja a létrehozott Dev Spaces-teret egy csapattaggal](how-to/share-dev-spaces.md), és megmutathatja neki, milyen egyszerű az együttműködés.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
