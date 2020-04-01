---
title: Csapatfejlesztés a Node.js és a Visual Studio Code használatával
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Dev Spaces és a Visual Studio Code segítségével a csapatfejlesztést egy Node.js alkalmazáson az Azure Kubernetes szolgáltatásban
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
ms.openlocfilehash: abcf4934af056d508ac136f80758597294d40b1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251937"
---
# <a name="team-development-using-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Csapatfejlesztés a Node.js és a Visual Studio-kód használatával az Azure Dev Spaces használatával

Ebben az oktatóanyagban megtudhatja, hogyan működhet együtt a fejlesztők egy csoportja ugyanabban a Kubernetes-fürtben a fejlesztői tárolóhelyek használatával.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása
Eddig úgy futtatta alkalmazása kódját, mintha csak Ön dolgozna fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Lehetővé teszi a fejlesztők egy csoportja, hogy ugyanabban a környezetben, a közös fejlesztési térben vagy a különböző fejlesztői terek, ha szükséges.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazása jelenleg nem túl összetett. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat. Hamar életszerűtlenné válhat, hogy mindent helyileg futtasson a fejlesztéshez.

* Előfordulhat, hogy a fejlesztői gép nem rendelkezik elegendő erőforrással az összes szükséges szolgáltatás egyszerre való futtatásához.
* Előfordulhat, hogy egyes szolgáltatásoknak nyilvánosan elérhetőnek kell lenniük. Előfordulhat például, hogy egy szolgáltatásnak rendelkeznie kell egy webhook-ra válaszoló végpontjával.
* Ha a szolgáltatások egy részét szeretné futtatni, ismernie kell az összes szolgáltatás közötti teljes függőségi hierarchiát. Ennek meghatározása nehéz lehet, különösen a szolgáltatások száma növekszik.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a megközelítés segíthet, de ezeknek a gútáknak a kezelése hamarosan hatással lehet a fejlesztési költségekre. Plusz, ez a megközelítés vezet a fejlesztési környezet keres nagyon különbözik a termelés, amely lehetővé teszi a finom hibákat kúszik be.
* Ebből következik, hogy bármilyen típusú integrációs tesztelés nehézzé válik. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Fejlesztői terek használata csapatfejlesztéshez
Mutassuk be ezeket az ötleteket egy konkrét példával *a webfrontend* -> *mywebapi* mintaalkalmazásunk használatával. Majd elképzelni egy forgatókönyvet, ahol a fejlesztő, Scott, kell, hogy a változás a *mywebapi* szolgáltatás, és *csak* ezt a szolgáltatást. A *webfrontend* nem kell módosítani a Scott frissítés részeként.

_A_ Fejlesztői tárolók használata nélkül Scott nak több módja is lenne a frissítés fejlesztésére és tesztelésére, amelyek egyike sem ideális:
* Futtassa az ÖSSZES összetevőt helyileg. Ehhez egy hatékonyabb fejlesztői gépre van szükség a Docker telepítve, és potenciálisan MiniKube.
* Futtassa az összes összetevőt egy elkülönített névtérben a Kubernetes-fürtön. Mivel *a webfrontend* nem változik, ez a fürt erőforrások pazarlása.
* CSAK futtatni *mywebapi*, és kézi REST hívások tesztelésére. Ez nem teszteli a teljes végpontok között folyamat.
* Fejlesztési-központú kód hozzáadása a *webfrontendhez,* amely lehetővé teszi a fejlesztő számára, hogy kéréseket küldjön a *mywebapi*egy másik példányának. Ez bonyolítja a *webfrontend* szolgáltatást.

### <a name="set-up-your-baseline"></a>Az alapterv beállítása
Először is fel kell vetnünk a szolgáltatásaink alapvonalát. Ez a központi telepítés az "utolsó ismert jót" fogja képviselni, így könnyen összehasonlíthatja a helyi kód viselkedését a beadott verzióval. Ezután létrehozunk egy gyermekteret ezen alapkonfiguráció alapján, így a nagyobb alkalmazás kontextusában tesztelhetjük a *mywebapi* módosításait.

1. Klónozza a [fejlesztői terek mintaalkalmazást:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Pénztár a távoli ág *azds_updates:*`git checkout -b azds_updates origin/azds_updates`
1. Válassza ki a `azds space select --name dev` _fejlesztői_ területet: . Amikor a program a szülőfejlesztési terület kiválasztását kéri, válassza a _ \<None\>_ lehetőséget.
1. Keresse meg a _mywebapi_ könyvtárat, és hajtsa végre:`azds up -d`
1. Keresse meg a _webfrontend könyvtárat,_ és hajtsa végre:`azds up -d`
1. Végrehajtás `azds list-uris` a _webfrontend_ nyilvános végpontjának megtekintéséhez

> [!TIP]
> A fenti lépések manuálisan állítsa be az alapkonfigurációt, de azt javasoljuk, hogy a csapatok CI/CD-t használjanak, hogy az alapterv automatikusan naprakész maradjon a véglegesített kóddal.
>
> Tekintse meg [a CI/CD azure DevOps-szal való beállításához készült útmutatónkat](how-to/setup-cicd.md) az alábbi diagramhoz hasonló munkafolyamat létrehozásához.
>
> ![Példa CI/CD-diagram](media/common/ci-cd-complex.png)

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

A DevSpace oszlop azt mutatja, hogy mindkét szolgáltatás egy _dev_nevű területen fut. Bárki, aki megnyitja a nyilvános URL-t, és a webalkalmazásra navigál, meghívja a mindkét szolgáltatáson keresztül futó beadott kódelérési utat. Most tegyük fel, hogy folytatni szeretné a _mywebapi fejlesztését._ Hogyan módosíthatja a kódokat, és tesztelheti őket úgy, hogy nem akadályozza a fejlesztői környezetet használó többi fejlesztőt? Ehhez be kell állítania a saját terét.

### <a name="create-a-dev-space"></a>Dev Spaces-tér létrehozása
Ha a _mywebapi_ saját verzióját nem _a fejlesztésen_szeretné futtatni, a következő paranccsal létrehozhatja saját tárhelyét:

```cmd
azds space select --name scott
```

Amikor a rendszer kéri, válassza a _fejlesztői terület_ **szülőfejlesztési területként**lehetőséget. Ez azt jelenti, hogy az új tér, _dev / scott_, fog származni a tér _dev_. Hamarosan kiderül, hogyan segít ez a tesztelésben.

Összhangban a bevezető hipotetikus, már használják a nevét _Scott_ az új helyet, így társaik azonosítani, aki dolgozik benne. De lehet nevezni, amit akarsz, és rugalmas, hogy mit jelent, mint _a sprint4_ vagy _demo_. Bármi legyen is a helyzet, _dev_ szolgál az alapkonfiguráció minden fejlesztő dolgozik egy darab az alkalmazás:

![](media/common/ci-cd-space-setup.png)

Futtassa az `azds space list` parancsot a fejlesztői környezetben lévő terek listájának megtekintéséhez. A _Kijelölt_ oszlop jelzi, hogy jelenleg melyik területet jelölte ki (igaz/hamis). Az Ön esetében a _dev/scott_ nevű terület automatikusan kiválasztásra került a létrehozáskor. Az `azds space select` paranccsal bármikor kiválaszthat egy másik teret.

Lássuk működés közben.

### <a name="make-a-code-change"></a>Kódmódosítás
Lépjen a VS-kód `mywebapi` ablakba, és készítsen egy `/` kódszerkesztést az alapértelmezett GET-kezelőhöz a alkalmazásban, `server.js`például:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>A szolgáltatás futtatása

A szolgáltatás futtatásához nyomja meg `azds up` az F5 (vagy írja be a terminálablak) parancsot a szolgáltatás futtatásához. A szolgáltatás automatikusan fut az újonnan kiválasztott tér _ben dev / scott_. Ellenőrizze, hogy a szolgáltatás a saját `azds list-up`tárhelyén fut-e a következő futtatásával:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Figyelje meg, hogy a *mywebapi* egy példánya most fut a _fejlesztői/scott-térben._ A _fejlesztői_ verzió továbbra is fut, de nem szerepel a listában.

Az aktuális terület URL-címének `azds list-uris`listázása a futtatásával.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Figyelje meg, hogy a *webfrontend* nyilvános hozzáférési pont URL-címe *a scott.s*előtaggal van ellátva. Ez az URL-cím a _fejlesztői/scott-téregyedi._ Ez az URL-előtag azt mondja a rendszera rendszerbeérkező kontrájának, hogy a kérelmeket egy szolgáltatás _fejlesztési/scott_ verziójához irányítsa. Ha egy ilyen URL-címet tartalmazó kérést a fejlesztői tárolók kezelik, a bejövő kapcsolatvezérlő először megpróbálja a kérést a _fejlesztői/scott-térben_ lévő *webfrontend* szolgáltatásra irányítani. Ha ez nem sikerül, a kérelem lesz irányítva a *webfrontend* szolgáltatás a _fejlesztői_ térben, mint egy tartalék. Azt is figyelje meg, hogy van egy localhost URL-cím a szolgáltatás helyi állomáson keresztül a Kubernetes *port forward* funkció használatával. Az Azure dev spaces-címekkel kapcsolatos URL-címekről és útválasztási lehetőségekről az [Azure dev spaces működésének és konfigurálásának módjában talál további információt.](how-dev-spaces-works.md)

![Térútválasztás](media/common/Space-Routing.png)

Az Azure Dev Spaces ezen beépített szolgáltatása lehetővé teszi, hogy tesztelhesse a kódot egy megosztott térben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját tere teljes szolgáltatási vermét. Ez az útválasztás megköveteli az alkalmazáskódtól a propagálási fejlécek továbbítását, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-in-a-space"></a>Kód tesztelése egy térben
A *mywebapi* új verziójának *webfrontenddel*való teszteléséhez nyissa meg a böngészőt a *webfrontend* nyilvános hozzáférési pont URL-címére, és lépjen a Beszólás lapra. Meg kell jelennie az új üzenetnek.

Most távolítsa el az URL-cím „scott.s.” előtagját, és frissítse a böngészőt. Meg kell jelennie a régi viselkedés (a *mywebapi* verzió fut _dev_).

Miután rendelkezik egy _fejlesztői_ terület, amely mindig tartalmazza a legújabb módosításokat, és feltételezve, hogy az alkalmazás célja, hogy kihasználják a DevSpace téralapú útválasztás az ebben az oktatóanyag-szakaszban leírtak szerint, remélhetőleg lesz könnyű belátni, hogyan Dev Spaces nagyban segíthet i új funkciók tesztelésében a nagyobb alkalmazás keretében. Ahelyett, hogy _az összes_ szolgáltatást a privát térben, létrehozhat egy privát helyet, amely származik _dev_, és csak "fel" a szolgáltatások éppen dolgozik. A fejlesztői tereket útválasztási infrastruktúra kezeli a többit kihasználva annyi szolgáltatást ki a privát tér, amennyit csak talál, miközben alapértelmezés szerint vissza a legújabb verziófut a _fejlesztői_ térben. És ami még jobb, _több_ fejlesztő is aktívan fejleszteni a különböző szolgáltatások ugyanabban az időben a saját térben megszakítása nélkül egymást.

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * A fejlesztési terek használatával hozzon létre egy alapkonfigurációt a funkciók használatával, amely ekkel egyszerűen tesztelheti az elkülönített módosításokat egy nagyobb mikroszolgáltatás-alkalmazás környezetében

Most, hogy felderítette az Azure Dev Spaces-t, [ossza meg a fejlesztői területet egy csapattaggal,](how-to/share-dev-spaces.md) és kezdjen el együttműködni.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
