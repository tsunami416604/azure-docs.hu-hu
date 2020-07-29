---
title: Csoportmunka-fejlesztés a Java és a Visual Studio Code használatával
services: azure-dev-spaces
author: stepro
ms.custom: devx-track-java
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure dev Spaces és a Visual Studio Code az Azure Kubernetes szolgáltatásban található Java-alkalmazásokban való csoportmunka-fejlesztéshez
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 80544b497caf39ee45acb82ff644a5c06ac3ff7e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327191"
---
# <a name="team-development-using-java-and-visual-studio-code-with-azure-dev-spaces"></a>Csoportmunka-fejlesztés a Java és a Visual Studio Code segítségével az Azure dev Spaces használatával

Ebből az oktatóanyagból megtudhatja, hogy a fejlesztők hogyan dolgozhatnak egyszerre ugyanazon a Kubernetes-fürtön a dev Spaces használatával.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása
Eddig úgy futtatta alkalmazása kódját, mintha csak Ön dolgozna fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Lehetővé teheti a fejlesztők számára, hogy ugyanabban a környezetben működjenek, ha a megosztott fejlesztői térben vagy a különböző fejlesztői területeken szükség szerint dolgozik.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazása jelenleg nem túl összetett. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat. Hamar életszerűtlenné válhat, hogy mindent helyileg futtasson a fejlesztéshez.

* Előfordulhat, hogy a fejlesztői gép nem rendelkezik elegendő erőforrással az összes szükséges szolgáltatás futtatásához.
* Előfordulhat, hogy egyes szolgáltatásoknak nyilvánosan elérhetőnek kell lenniük. Előfordulhat például, hogy egy szolgáltatásnak rendelkeznie kell egy webhookra válaszoló végponttal.
* Ha a szolgáltatások egy részhalmazát szeretné futtatni, ismernie kell a teljes függőségi hierarchiát az összes szolgáltatás között. Ennek megállapítása nehéz lehet, különösen, ha a szolgáltatások száma növekszik.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a megközelítés segíthet, de az ilyen modellek kezelése hamarosan hatással lehet a fejlesztési díjakra. Ezzel a megközelítéssel a fejlesztési környezet nagyon eltérhet a termeléstől, ami lehetővé teszi a finom hibákat a becsúszáshoz.
* Ebből következik, hogy az integrációs tesztek bármilyen típusát megnehezíti. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![A szolgáltatások fejlesztése során felmerülő kihívásokat ábrázoló diagram.](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Fejlesztői területek használata a csapatmunkához
Bemutatjuk ezeket az ötleteket egy konkrét példával a *webfrontend*  ->  *mywebapi* -minta alkalmazás használatával. Képzeljük el, hogy egy fejlesztő, Scott, a *mywebapi* szolgáltatást kell módosítania, és *csak* ezt a szolgáltatást kell használnia. A *webfrontend* nem kell változtatnia a Scott frissítésének részeként.

A dev Spaces használata _nélkül_ Scott számos módon fejlesztheti és tesztelheti a frissítését, amelyek közül egyik ideális megoldás:
* MINDEN összetevőt helyileg futtasson. Ehhez a Docker által telepített és potenciálisan MiniKube nagyobb teljesítményű fejlesztési gép szükséges.
* Futtassa az összes összetevőt egy elkülönített névtérben a Kubernetes-fürtön. Mivel a *webfrontend* nem változik, a fürt erőforrásainak hulladéka.
* CSAK futtasson *mywebapi*, és VÉGEZZEN manuális Rest-hívásokat a teszteléshez. Ez nem teszteli a teljes végpontok közötti folyamatot.
* Bővítse a fejlesztéssel célzott kódot a *webfrontendhez* , amely lehetővé teszi, hogy a fejlesztő kéréseket küldjön a *mywebapi*egy másik példányára. Ez bonyolítja a *webfrontend* szolgáltatást.

### <a name="set-up-your-baseline"></a>Az Alapterv beállítása
Először üzembe kell helyeznie a szolgáltatásaink alapkonfigurációját. Ez a központi telepítés a "legutóbbi ismert jó" állapotot fogja tartalmazni, így egyszerűen összehasonlíthatja a helyi kód viselkedését és a bejelentkezett verziót. Ezután létrehozunk egy gyermek területet ezen alapkonfiguráció alapján, hogy a nagyobb alkalmazás kontextusában teszteljük a *mywebapi* módosításait.

1. A [dev Spaces minta alkalmazás](https://github.com/Azure/dev-spaces)klónozása:`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. A távoli ág *azds_updates*:`git checkout -b azds_updates origin/azds_updates`
1. Válassza ki a _fejlesztői_ területet: `azds space select --name dev` . Amikor a rendszer rákérdez a szülő fejlesztői terület kiválasztására, válassza a elemet _\<none\>_ .
1. Navigáljon a _mywebapi_ könyvtárba, és hajtsa végre a következőt:`azds up -d`
1. Navigáljon a _webfrontend_ könyvtárához, és hajtsa végre a következőt:`azds up -d`
1. Végrehajtás a `azds list-uris` _webfrontend_ nyilvános végpontjának megtekintéséhez

> [!TIP]
> A fenti lépések manuálisan állítanak be egy alapkonfigurációt, de azt javasoljuk, hogy a Teams a CI/CD használatával automatikusan tartsa naprakészen az alapkonfigurációt a véglegesített kóddal.
>
> Az alábbi ábrához hasonló munkafolyamat létrehozásához tekintse meg a [CI/CD beállítása az Azure DevOps című útmutatót](how-to/setup-cicd.md) .
>
> ![Példa CI/CD diagramra](media/common/ci-cd-complex.png)

Ezen a ponton az alaptervnek futnia kell. Futtassa az `azds list-up --all` parancsot. A következőhöz hasonló kimenet fog megjelenni:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A DevSpace oszlop azt mutatja, hogy mindkét szolgáltatás egy _dev_nevű térben fut. Bárki, aki megnyitja a nyilvános URL-címet, és a webalkalmazásra navigál, meghívja a bejelentkezett kód elérési útját, amely mindkét szolgáltatáson keresztül fut. Most tegyük fel, hogy folytatni szeretné a _mywebapi_fejlesztését. Hogyan módosíthatja a kódokat, és tesztelheti őket úgy, hogy nem akadályozza a fejlesztői környezetet használó többi fejlesztőt? Ehhez be kell állítania a saját terét.

### <a name="create-a-dev-space"></a>Dev Spaces-tér létrehozása
Ha a _mywebapi_ saját verzióját szeretné futtatni a _dev_-től eltérő helyen, a következő paranccsal hozhatja létre a saját tárhelyét:

```cmd
azds space select --name scott
```

Ha a rendszer kéri, válassza a _dev_ elemet a **szülő fejlesztői területként**. Ez azt jelenti, hogy az új tárhely, a _dev/Scott_a Space _dev_-ből származik. Hamarosan kiderül, hogyan segít ez a tesztelésben.

A bevezető feltételnek megfelelően a _Scott_ nevet használtuk az új tárhelyhez, hogy a társak azonosítani tudják, ki dolgozik. De lehet meghívni, amit szeretne, és rugalmasan láthatja, hogy mit jelent, mint például a _sprint4_ vagy a _demó_. Függetlenül attól, hogy a _dev_ az alkalmazás egy részén dolgozó összes fejlesztő alapkonfigurációként szolgál:

![Egy egyszerű C I C-hely beállítását bemutató diagram.](media/common/ci-cd-space-setup.png)

Futtassa az `azds space list` parancsot a fejlesztői környezetben lévő terek listájának megtekintéséhez. A _kijelölt_ oszlop jelzi, hogy melyik terület van kiválasztva (TRUE/FALSE). Ebben az esetben a _dev/Scott_ nevű terület automatikusan ki lett választva a létrehozásakor. Az `azds space select` paranccsal bármikor kiválaszthat egy másik teret.

Lássuk működés közben.

### <a name="make-a-code-change"></a>Kód módosításának elkészítése
Nyissa meg a VS Code ablakot, `mywebapi` és végezze el a kód szerkesztését a `String index()` metódushoz `src/main/java/com/ms/sample/mywebapi/Application.java` , például:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

### <a name="run-the-service"></a>A szolgáltatás futtatása

A szolgáltatás futtatásához nyomja le az F5 billentyűt (vagy írja be `azds up` a terminál ablakát) a szolgáltatás futtatásához. A szolgáltatás automatikusan futni fog az újonnan kiválasztott, _dev/Scott_-beli helyen. A futtatásával ellenőrizze, hogy a szolgáltatás a saját területén fut-e `azds list-up` :

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Figyelje meg, hogy a *mywebapi* egy példánya már fut a _fejlesztői/Scott_ -térben. A _dev_ -ben futó verzió még fut, de nem szerepel a listáján.

Az aktuális terület URL-címeinek listázása a futtatásával `azds list-uris` .

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Figyelje meg, hogy a *webfrontend* nyilvános hozzáférési pontjának URL-címe a *Scott. s*előtaggal van ellátva. Ez az URL-cím a _dev/Scott_ -terület egyedi. Ez az URL-előtag közli a bejövő vezérlővel, hogy átirányítsa a kéréseket egy szolgáltatás _dev/Scott-_ verziójára. Ha az ezzel az URL-címmel rendelkező kérelmet a dev Spaces kezeli, akkor a bejövő vezérlő először megkísérli a kérés átirányítását a *webfrontend* szolgáltatásba a _dev/Scott_ területen. Ha ez nem sikerül, a rendszer tartalékként továbbítja a kérést a *webfrontend* szolgáltatásnak a _fejlesztői_ térben. Azt is figyelje meg, hogy van-e localhost URL-cím a szolgáltatás eléréséhez a localhost-on keresztül a Kubernetes *-port továbbítására* szolgáló funkció használatával. További információ az URL-címekről és az útválasztásról az Azure dev Spaces szolgáltatásban: az [Azure dev Spaces működése és konfigurálása](how-dev-spaces-works.md).

![Hely útválasztása](media/common/Space-Routing.png)

Az Azure Dev Spaces ezen beépített szolgáltatása lehetővé teszi, hogy tesztelhesse a kódot egy megosztott térben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját tere teljes szolgáltatási vermét. Ez az útválasztás megköveteli az alkalmazáskódtól a propagálási fejlécek továbbítását, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-in-a-space"></a>Kód tesztelése egy térben
Ha tesztelni szeretné a *mywebapi* új verzióját a *webfrontend*használatával, nyissa meg a böngészőt a *webfrontend* nyilvános hozzáférési pontjának URL-címében, és lépjen a névjegy lapra. Meg kell jelennie az új üzenetnek.

Most távolítsa el az URL-cím „scott.s.” előtagját, és frissítse a böngészőt. Ekkor meg kell jelennie a régi viselkedésnek (a _dev_-ben futó *mywebapi* -verzióval).

Ha rendelkezik egy _fejlesztői_ területtel, amely mindig tartalmazza a legújabb módosításokat, és feltételezi, hogy az alkalmazás úgy van kialakítva, hogy kihasználja a DevSpace-alapú útválasztást az oktatóanyag szakaszban leírtak szerint, remélhetőleg könnyen megtekintheti, hogy a fejlesztői helyek hogyan segíthetnek a nagyobb alkalmazások környezetében rejlő új szolgáltatások tesztelésében. Ahelyett, hogy az _összes_ szolgáltatást a saját tárhelyére kellene telepítenie, létrehozhat egy olyan privát helyet, amely a _dev_-ből származik, és csak a ténylegesen használt szolgáltatások közül választhat. A dev Spaces útválasztási infrastruktúrája a REST-t úgy fogja kezelni, hogy a saját tárterületének számos szolgáltatását használja, miközben a keresés közben a legújabb verzióra, a _fejlesztői_ térben pedig az alapértelmezett értékre kerül. És még jobb, ha _több_ fejlesztő is aktívan fejleszti a különböző szolgáltatásokat a saját területén anélkül, hogy megzavarja egymást.

### <a name="well-done"></a>Szép munka!
Elvégezte az első lépéseket ismertető útmutatót! Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * A fejlesztői Spaces használatával egyszerűen tesztelheti az elkülönített módosításokat egy nagyobb, nagy teljesítményű szolgáltatásalkalmazás környezetében.

Most, hogy már megvizsgálta az Azure fejlesztői tárhelyeit, [megoszthatja a fejlesztői területét egy csapattagtal](how-to/share-dev-spaces.md) , és megkezdheti az együttműködését.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
