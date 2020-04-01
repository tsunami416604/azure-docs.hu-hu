---
title: Csapatfejlesztés a .NET Core és a Visual Studio használatával
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Dev Spaces és a Visual Studio segítségével a .NET Core alkalmazások csapatfejlesztését az Azure Kubernetes szolgáltatásban
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
ms.openlocfilehash: b4520ce35807fb022fa39ae9b00347a27e192380
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78245054"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Csapatfejlesztés a .NET Core és a Visual Studio használatával az Azure Dev Spaces segítségével

Ebben az oktatóanyagban megtudhatja, hogyan működhet együtt a fejlesztők egy csoportja ugyanabban a Kubernetes-fürtben a fejlesztői tárolóhelyek használatával.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

Eddig úgy futtatta alkalmazása kódját, mintha Ön dolgozna egyedül fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Lehetővé teszi a fejlesztők egy csoportja, hogy ugyanabban a környezetben, a közös fejlesztési térben vagy a különböző fejlesztői terek, ha szükséges.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazás jelenleg nem bonyolult. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat.

* Előfordulhat, hogy a fejlesztői gép nem rendelkezik elegendő erőforrással az összes szükséges szolgáltatás egyszerre való futtatásához.
* Előfordulhat, hogy egyes szolgáltatásoknak nyilvánosan elérhetőnek kell lenniük. Előfordulhat például, hogy egy szolgáltatásnak rendelkeznie kell egy webhook-ra válaszoló végpontjával.
* Ha a szolgáltatások egy részét szeretné futtatni, ismernie kell az összes szolgáltatás közötti teljes függőségi hierarchiát. A hierarchia meghatározása nehéz lehet, különösen a szolgáltatások számának növekedésével.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a megközelítés segíthet, de ezeknek a gútáknak a kezelése hamarosan hatással lehet a fejlesztési költségekre. Plusz, ez a megközelítés vezet a fejlesztői környezet keres más, mint a termelés, ami oda vezethet, hogy finom hibák fordulnak elő.
* Ebből következik, hogy bármilyen típusú integrációs tesztelés nehézzé válik. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Fejlesztői terek használata csapatfejlesztéshez
Mutassuk be ezeket az ötleteket egy konkrét példával *a webfrontend* -> *mywebapi* mintaalkalmazásunk használatával. Majd elképzelni egy forgatókönyvet, ahol a fejlesztő, Scott, kell, hogy a változás a *mywebapi* szolgáltatás, és *csak* ezt a szolgáltatást. A *webfrontend* nem kell módosítani a Scott frissítés részeként.

_A_ Fejlesztői tárolók használata nélkül Scott nak több módja is lenne a frissítés fejlesztésére és tesztelésére, amelyek egyike sem ideális:
* Futtassa az összes összetevőt helyileg, amely nagyobb teljesítményű fejlesztői gépet igényel a Docker telepítve, és potenciálisan MiniKube.
* Futtassa az összes összetevőt egy elkülönített névtérben a Kubernetes-fürtön. Mivel *a webfrontend* nem változik, egy elszigetelt névtér használata a fürt erőforrások pazarlása.
* CSAK futtatni *mywebapi*, és kézi REST hívások tesztelésére. Az ilyen típusú tesztelés nem teszteli a teljes végpontok között folyamat.
* Fejlesztési-központú kód hozzáadása a *webfrontendhez,* amely lehetővé teszi a fejlesztő számára, hogy kéréseket küldjön a *mywebapi*egy másik példányának. A kód hozzáadása bonyolítja a *webfrontend* szolgáltatást.

### <a name="set-up-your-baseline"></a>Az alapterv beállítása
Először is fel kell vetnünk a szolgáltatásaink alapvonalát. Ez a központi telepítés az "utolsó ismert jót" fogja képviselni, így könnyen összehasonlíthatja a helyi kód viselkedését a beadott verzióval. Ezután létrehozunk egy gyermekteret ezen alapkonfiguráció alapján, így a nagyobb alkalmazás kontextusában tesztelhetjük a *mywebapi* módosításait.

1. Klónozza a [fejlesztői terek mintaalkalmazást:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Nézze meg a távoli ág *azds_updates:*`git checkout -b azds_updates origin/azds_updates`
1. Zárjon be minden F5/hibakeresési munkamenetet minden szolgáltatás esetében, de tartsa nyitva a projekteket saját Visual Studio-ablakukban.
1. Váltás a Visual Studio ablakára a _mywebapi_ projekttel.
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
1. Válassza a **Módosítás** lehetőséget, ha létre szeretné hozni azt a helyet, amelyet az F5 vagy a Ctrl+F5 billentyűkombinációban használ.
1. A Szóköz legördülő menüben válassza ** \<az Új terület létrehozása lehetőséget... \>**.
1. Győződjön meg arról, hogy **dev** ** \<\>** a szülőterület nincs, és írja be a terület nevét dev . Kattintson az OK gombra.
1. Nyomja le a Ctrl+F5 billentyűkombinációt a _mywebapi_ futtatásához a hibakereső csatlakoztatása nélkül.
1. Váltson a _Webfrontend_ projekttel rendelkező Visual Studio-ablakra, és a Ctrl+F5 billentyűkombinációval futtassa azt is.

> [!Note]
> Néha frissíteni kell a böngészőlapot azt követően, hogy a weboldal először megjelenik a Ctrl+F5 lenyomása után.

> [!TIP]
> A fenti lépések manuálisan állítsa be az alapkonfigurációt, de azt javasoljuk, hogy a csapatok CI/CD-t használjanak, hogy az alapterv automatikusan naprakész maradjon a véglegesített kóddal.
>
> Tekintse meg [a CI/CD azure DevOps-szal való beállításához készült útmutatónkat](how-to/setup-cicd.md) az alábbi diagramhoz hasonló munkafolyamat létrehozásához.
>
> ![Példa CI/CD-diagram](media/common/ci-cd-complex.png)

Bárki, aki megnyitja a nyilvános URL-t, és a webalkalmazásra navigál, meghívja az Ön által írt kódelérési utat, amely mindkét szolgáltatáson keresztül fut az alapértelmezett _fejlesztői_ terület használatával. Most tegyük fel, hogy folytatni szeretné a *mywebapi* fejlesztését - hogyan teheti ezt meg, és nem szakíthatja meg a többi fejlesztőt, akik a fejlesztői teret használják? Ehhez be kell állítania a saját terét.

### <a name="create-a-new-dev-space"></a>Új Dev Spaces-tér létrehozása
A Visual Studióból létrehozhat további tereket, amelyek akkor lesznek használatban, ha a szolgáltatásában lenyomja az F5, illetve a Ctrl+F5 billentyűt. Olyan nevet adhat a tereknek, amilyet csak szeretne, és rugalmasan kezelheti a jelentését (pl.: _sprint4_ vagy _demo_).

Új teret a következő módon hozhat létre:
1. Váltás a Visual Studio ablakára a *mywebapi* projekttel.
2. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
3. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
4. Itt módosíthatja vagy létrehozhatja azt a fürtöt és/vagy teret, amelyet a program akkor használ, ha lenyomja az F5, illetve a Ctrl+F5 billentyűt. *Győződjön meg róla, hogy a korábban létrehozott Azure Dev Spaces-tér van kiválasztva*.
5. A Szóköz legördülő menüben válassza ** \<az Új terület létrehozása lehetőséget... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. A **Szóköz hozzáadása** párbeszédpanelen állítsa be a **szülőterületet a fejlesztői**térre, és adja meg az új terület nevét. Használhatja a saját nevét (pl.: „scott”) az új térhez, így munkatársai számára is könnyen beazonosítható a munkájához használt tér. Kattintson az **OK** gombra.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Most már láthatja az AKS-fürtöt és az új, a projekttulajdonságok oldalán kiválasztott teret.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Kód frissítése a *mywebapi*-hoz

1. A *mywebapi* projekt, hogy a `string Get(int id)` kód `Controllers/ValuesController.cs` módosítása a módszer a fájlban az alábbiak szerint:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Állítson be egy töréspontot a frissített kódrészletben (elképzelhető, hogy korábban már megadott egyet).
3. Nyomja meg az F5 billentyűt a _mywebapi_ szolgáltatás elindításához, amely a kijelölt terület használatával indítja el a szolgáltatást a fürtben. A kijelölt hely ebben az esetben _scott_.

Ez az ábra segít megérteni, hogyan működnek a különféle terek. A lila elérési út egy kérelmet jelenít meg a _fejlesztési_ területen keresztül, amely az alapértelmezett elérési út, ha nincs térköz az URL-cím. A rózsaszín elérési út a _fejlesztői/scott-területen_ keresztüli kérést jelenít meg.

![](media/common/Space-Routing.png)

Az Azure Dev Spaces e beépített funkciója lehetővé teszi, hogy végpontok között tesztelhesse a kódot egy megosztott környezetben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját terük teljes szolgáltatási vermét. Ez az útválasztás megköveteli, hogy a propagálási fejlécek továbbítva legyenek az alkalmazáskódban, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-running-in-the-_devscott_-space"></a>A _fejlesztési/scott_ térben futó tesztkód
Ha a *mywebapi* új verzióját a *webfrontenddel*együtt szeretné tesztelni, nyissa meg a http://dev.webfrontend.123456abcdef.eus.azds.io) böngészőt a *webfrontend* nyilvános hozzáférési pont URL-címére (például a Betakar ásólapra. Az eredeti, „Hello from webfrontend and Hello from mywebapi” üzenetnek kell megjelennie.

Most adja hozzá a „scott.s” kifejezést része az URL-t, így\:olvas valamit, mint http / / scott.s.dev.webfrontend.123456abcdef.eus.azds.io és frissítse a böngészőt. A *mywebapi* projektben beállított töréspontnak meg kell jelennie. Nyomja le az F5-öt a folytatáshoz. A böngészőben megjelenik az új üzenet: „Hello from webfrontend and mywebapi now says something new.” Ennek az az oka, hogy a *mywebapi* frissített kódjának elérési útja a _fejlesztői/scott-térben_ fut.

Miután rendelkezik egy _fejlesztői_ terület, amely mindig tartalmazza a legújabb módosításokat, és feltételezve, hogy az alkalmazás célja, hogy kihasználják a DevSpace téralapú útválasztás az ebben az oktatóanyag-szakaszban leírtak szerint, remélhetőleg lesz könnyű belátni, hogyan fejlesztői terek nagyban segítheti az új funkciók tesztelését a nagyobb alkalmazás keretében. Ahelyett, hogy _az összes_ szolgáltatást a privát térben, létrehozhat egy privát helyet, amely származik _dev_, és csak "fel" a szolgáltatások éppen dolgozik. A fejlesztői tereket útválasztási infrastruktúra kezeli a többit kihasználva annyi szolgáltatást ki a privát tér, amennyit csak talál, miközben alapértelmezés szerint vissza a legújabb verziófut a _fejlesztői_ térben. És ami még jobb, _több_ fejlesztő is aktívan fejleszteni a különböző szolgáltatások ugyanabban az időben a saját térben megszakítása nélkül egymást.

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * A fejlesztési terek használatával hozzon létre egy alapkonfigurációt a funkciók használatával, amely ekkel egyszerűen tesztelheti az elkülönített módosításokat egy nagyobb mikroszolgáltatás-alkalmazás környezetében

Most, hogy megismerte az Azure Dev Spacest, [megoszthatja a létrehozott Dev Spaces-teret egy csapattaggal](how-to/share-dev-spaces.md), és megmutathatja neki, milyen egyszerű az együttműködés.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
