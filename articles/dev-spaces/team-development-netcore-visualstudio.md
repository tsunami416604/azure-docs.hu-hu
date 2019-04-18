---
title: .NET Core és a Visual Studio használatával Azure-fejlesztési szóközt tartalmazó csoportos fejlesztése
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
ms.openlocfilehash: 58807aa5a540de6eaa9a337caa0c34fee9408296
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361449"
---
# <a name="team-development-with-azure-dev-spaces"></a>Csoportos fejlesztés az Azure Dev Spaces használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan egy fejlesztői csapat egyidejűleg együttműködhetnek fejlesztési szóközzel azonos Kubernetes-fürtben.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

Eddig úgy futtatta alkalmazása kódját, mintha Ön dolgozna egyedül fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Engedélyezze egy fejlesztői csapatra ugyanabban a környezetben, mivel egy közös fejlesztési szóközt vagy a különböző fejlesztési tárolóhelyek igény szerint működjön.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazás jelenleg nem bonyolult. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat.

* A fejlesztői gépén nem rendelkezhet egyszerre kell minden egyes szolgáltatás futtatásához elegendő erőforrással.
* Bizonyos szolgáltatásokat is el kell nyilvánosan elérhetőnek kell lennie. Előfordulhat, hogy egy szolgáltatás például, hogy egy végpontot, amely válaszol a webhook kell.
* Ha szeretné futtatni a szolgáltatások egy részhalmaza, azok tudja a teljes függőségi hierarchia közötti összes szolgáltatást. Ezt a hierarchiát meghatározása nehéz, lehet, különösen, ha a szolgáltatások számának növeléséhez.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a megközelítés segít, de ezek mocks kezelése hamarosan befolyásolhatja a fejlesztési költségeket. Emellett a fejlesztési környezet különbözik a production, ami kisebb hibák előforduló keresi ezt a megközelítést vezet.
* Ebből az következik, hogy nehéz bármilyen adattípus könnyű integráció tesztelése során válik. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Fejlesztői szóközökkel csoportos fejlesztéshez
Nézzük bemutatják, ezeket egy konkrét példa az ötleteit a *webfrontend* -> *mywebapi* mintaalkalmazást. Azt fogjuk imagine egy forgatókönyvet, ahol, Scott, a fejlesztőnek módosítja a *mywebapi* szolgáltatást, és *csak* szolgáltatás. A *webfrontend* Scott frissítés részeként módosítása nem szükséges.

_Nélkül_ fejlesztési szóközzel, Scott kell fejlesztése és tesztelése a frissítés néhány módszer, amelynek sem ideális:
* Az összes összetevőt helyileg, amelyhez szükség van egy nagyobb teljesítményű fejlesztői gépen a Docker telepítve van, és potenciálisan MiniKube futtathatja.
* Az összes összetevőt futtathatja egy elkülönített névtér a Kubernetes-fürtön. Mivel *webfrontend* nem változnak, használatával egy elkülönített névtere fürterőforrások feleslegesen használna.
* CSAK futtatni *mywebapi*, és győződjön meg arról, manuális REST-hívások tesztelése. Az ilyen tesztelést nem ellenőrzi a teljes – teljes körű folyamatot.
* Adja hozzá kódot fejlesztéssel foglalkozó *webfrontend* , amely lehetővé teszi, hogy a fejlesztő-kérelmeket küldjön egy másik példánya *mywebapi*. Ez a kód hozzáadása bonyolultabbá teszi a *webfrontend* szolgáltatás.

### <a name="set-up-your-baseline"></a>Az alapkonfiguráció beállítása
Először azt telepítenie kell egy alapkonfiguráció-szolgáltatásaink. A központi telepítés "utolsó ismert jó" képviseli, így egyszerűen össze lehessen hasonlítani a be van jelölve a verzió és helyi kódját viselkedését. Ezután létrehozunk egy gyermek hely alapján ez a alapvető, így tudjuk tesztelheti a módosításokat *mywebapi* a nagyobb alkalmazás összefüggésében.

1. Klónozás a [fejlesztési tárolóhelyek mintaalkalmazás](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. A távoli ág átvétele *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Zárjon be minden F5/hibakeresési munkamenetet minden szolgáltatás esetében, de tartsa nyitva a projekteket saját Visual Studio-ablakukban.
1. A Visual Studio ablakban váltson a _mywebapi_ projekt.
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
1. Válassza ki **módosítása** hozhat létre a helyet, amely lesz használni, az F5 billentyűt, vagy a Ctrl + F5 a szolgáltatást.
1. A terület legördülő listában válassza ki a  **\<új tárhely létrehozása... \>**.
1. Ellenőrizze, hogy a fölérendelt hely értéke  **\<nincs\>**, és adja meg a hely nevét **fejlesztési**. Kattintson az OK gombra.
1. Nyomja le a Ctrl + F5 futtatásához _mywebapi_ hibakeresővel nélkül.
1. A Visual Studio ablakban váltson a _webfrontend_ projektre, és nyomja le a Ctrl + F5 is futtatható.

> [!Note]
> Néha frissíteni kell a böngészőlapot azt követően, hogy a weboldal először megjelenik a Ctrl+F5 lenyomása után.

> [!TIP]
> Az alapterv kézi beállítása a fenti lépéseket, de a CI/CD – automatikus naprakészen a referenciakonfiguráció véglegesített kód teams használatát javasoljuk.
>
> Tekintse meg a [útmutató az Azure DevOps CI/CD beállítása](how-to/setup-cicd.md) munkafolyamat létrehozása a következő ábra hasonló.
>
> ![Példa a CI/CD-diagram](media/common/ci-cd-complex.png)

Bárki, aki megnyitja az URL-címe és a web app navigál meghívja a kódelérési út hozott melyik futtatások keresztül mindkét szolgáltatás az alapértelmezett _fejlesztési_ terület. Most tegyük fel, hogy folytatja a fejlődő *mywebapi* – hogyan lehet ezt megteszi, és nem megszakító más a fejlesztési helyet használó fejlesztők? Ehhez be kell állítania a saját terét.

### <a name="create-a-new-dev-space"></a>Új Dev Spaces-tér létrehozása
A Visual Studióból létrehozhat további tereket, amelyek akkor lesznek használatban, ha a szolgáltatásában lenyomja az F5, illetve a Ctrl+F5 billentyűt. Olyan nevet adhat a tereknek, amilyet csak szeretne, és rugalmasan kezelheti a jelentését (pl.: _sprint4_ vagy _bemutató_).

Új teret a következő módon hozhat létre:
1. A Visual Studio ablakban váltson a *mywebapi* projekt.
2. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
3. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
4. Itt módosíthatja vagy létrehozhatja azt a fürtöt és/vagy teret, amelyet a program akkor használ, ha lenyomja az F5, illetve a Ctrl+F5 billentyűt. *Győződjön meg róla, hogy a korábban létrehozott Azure Dev Spaces-tér van kiválasztva*.
5. A terület legördülő listában válassza ki a  **\<új tárhely létrehozása... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Az a **terület hozzáadása** párbeszédpanelen beállítása a fölérendelt hely **fejlesztési**, és adjon meg egy nevet az új helyet. Használhatja a saját nevét (pl.: „scott”) az új térhez, így munkatársai számára is könnyen beazonosítható a munkájához használt tér. Kattintson az **OK** gombra.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Most már láthatja az AKS-fürtöt és az új, a projekttulajdonságok oldalán kiválasztott teret.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Kód frissítése a *mywebapi*-hoz

1. Az a *mywebapi* projekt győződjön meg arról, váltson egy kódot a `string Get(int id)` metódus az fájl `Controllers/ValuesController.cs` módon:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Állítson be egy töréspontot a frissített kódrészletben (elképzelhető, hogy korábban már megadott egyet).
3. Nyomja le az F5 elindításához a _mywebapi_ szolgáltatás, amely saját fürtjében, a kiválasztott hely fogja elindítani a szolgáltatást. Ebben az esetben van a kiválasztott hely _scott_.

Ez az ábra segít megérteni, hogyan működnek a különféle terek. A lila elérési út használatával egy kérelem mutatja a _fejlesztési_ területet, amely az alapértelmezett elérési utat használja, ha nincs szabad hely van kiegészített az URL-címre. A rózsaszín elérési út használatával egy kérelem mutatja a _dev/scott_ terület.

![](media/common/Space-Routing.png)

Az Azure Dev Spaces e beépített funkciója lehetővé teszi, hogy végpontok között tesztelhesse a kódot egy megosztott környezetben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját terük teljes szolgáltatási vermét. Ez az útválasztás megköveteli, hogy a propagálási fejlécek továbbítva legyenek az alkalmazáskódban, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-running-in-the-devscott-space"></a>Tesztelje a kódot futtató a _dev/scott_ terület
Új verziójának tesztelése *mywebapi* együtt *webfrontend*, nyilvános hozzáférési pont URL-CÍMÉT a böngészőben nyissa meg *webfrontend* (például http://dev.webfrontend.123456abcdef.eus.azds.io), és lépjen a névjegy lapra. Az eredeti, „Hello from webfrontend and Hello from mywebapi” üzenetnek kell megjelennie.

Most adja hozzá a „scott.s” kifejezést az URL-címhez. amelynek így kell kinéznie: http://scott.s.dev.webfrontend.123456abcdef.eus.azds.io. Majd frissítse a böngészőoldalt. A töréspont állítsa be a *mywebapi* kattintson beolvasni a projekt. Nyomja le az F5-öt a folytatáshoz. A böngészőben megjelenik az új üzenet: „Hello from webfrontend and mywebapi now says something new.” Ennek az az oka az elérési útját a frissített kód *mywebapi* fut-e a _dev/scott_ terület.

Ha már van egy _fejlesztési_ mindig tartalmazza a legutóbbi módosításoknak, és ha az alkalmazás-terület az célja, hogy kihasználhatja DevSpace a terület-alapú útválasztást, az oktatóanyag ezen szakaszában leírtak szerint, remélhetőleg könnyen látható lesz milyen fejlesztési tárolóhelyek nagy mértékben végrehajtásában segít a nagyobb alkalmazás környezetében lévő új szolgáltatások tesztelése. Ahelyett, hogy üzembe helyezéséhez _összes_ szolgáltatások a személyes tárhelyre, létrehozhat egy privát területet származó _fejlesztési_, és "csak be" a ténylegesen dolgozik a szolgáltatásokat. A fejlesztői, szóközök útválasztási infrastruktúra fogja kezelni a többi felügyelniük annyi szolgáltatások a privát lemezterületét talál, alapértelmezés szerint a legújabb verzióra futtatása közben a _fejlesztési_ terület. Továbbra is, hatékonyabb és _több_ fejlesztők aktívan fejleszthetnek különböző szolgáltatásokat egy időben a saját térben egymással megszakítása nélkül.

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * Fejlesztői tárolóhelyek használatával egyszerűen tesztelheti az elkülönített módosításokat egy nagyobb mikroszolgáltatás-alkalmazás összefüggésében funkció megállapítja az alapértékeket

Most, hogy megismerte az Azure Dev Spacest, [megoszthatja a létrehozott Dev Spaces-teret egy csapattaggal](how-to/share-dev-spaces.md), és megmutathatja neki, milyen egyszerű az együttműködés.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
