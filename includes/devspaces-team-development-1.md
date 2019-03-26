---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439532"
---
# <a name="team-development-with-azure-dev-spaces"></a>Csoportos fejlesztés az Azure Dev Spaces használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan egy fejlesztői csapat egyidejűleg együttműködhetnek fejlesztési szóközzel azonos Kubernetes-fürtben.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása
Eddig úgy futtatta alkalmazása kódját, mintha csak Ön dolgozna fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Engedélyezze egy fejlesztői csapatra ugyanabban a környezetben, mivel egy közös fejlesztési szóközt vagy a különböző fejlesztési tárolóhelyek igény szerint működjön.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazása jelenleg nem túl összetett. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat. Hamar életszerűtlenné válhat, hogy mindent helyileg futtasson a fejlesztéshez.

* A fejlesztői gépén nem rendelkezhet egyszerre kell minden egyes szolgáltatás futtatásához elegendő erőforrással.
* Bizonyos szolgáltatásokat is el kell nyilvánosan elérhetőnek kell lennie. Előfordulhat, hogy egy szolgáltatás például, hogy egy végpontot, amely válaszol a webhook kell.
* Ha szeretné futtatni a szolgáltatások egy részhalmaza, azok tudja a teljes függőségi hierarchia közötti összes szolgáltatást. Annak meghatározásához, nehéz, különösen, a szolgáltatások számának növelése.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a megközelítés segít, de ezek mocks kezelése hamarosan befolyásolhatja a fejlesztési költségeket. Emellett a fejlesztési környezet jelentősen különbözik a production, ami lehetővé teszi, hogy kötés finom központnak keresi ezt a megközelítést vezet.
* Ebből az következik, hogy nehéz bármilyen adattípus könnyű integráció tesztelése során válik. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Fejlesztői szóközökkel csoportos fejlesztéshez
Nézzük bemutatják, ezeket egy konkrét példa az ötleteit a *webfrontend* -> *mywebapi* mintaalkalmazást. Azt fogjuk imagine egy forgatókönyvet, ahol, Scott, a fejlesztőnek módosítja a *mywebapi* szolgáltatást, és *csak* szolgáltatás. A *webfrontend* Scott frissítés részeként módosítása nem szükséges.

_Nélkül_ fejlesztési szóközzel, Scott kell fejlesztése és tesztelése a frissítés néhány módszer, amelynek sem ideális:
* Az összes összetevő helyi futtatásához. Ehhez egy nagyobb teljesítményű fejlesztői gépen a Docker telepítve van, és potenciálisan MiniKube.
* Az összes összetevőt futtathatja egy elkülönített névtér a Kubernetes-fürtön. Mivel *webfrontend* nem változnak, ez a fürterőforrások feleslegesen használna.
* CSAK futtatni *mywebapi*, és győződjön meg arról, manuális REST-hívások tesztelése. Ez a teljes, végpontok közötti folyamat nem teszteléséhez.
* Adja hozzá kódot fejlesztéssel foglalkozó *webfrontend* , amely lehetővé teszi, hogy a fejlesztő-kérelmeket küldjön egy másik példánya *mywebapi*. Ez bonyolultabbá teszi a *webfrontend* szolgáltatás.

### <a name="set-up-your-baseline"></a>Az alapkonfiguráció beállítása
Először azt telepítenie kell egy alapkonfiguráció-szolgáltatásaink. A központi telepítés "utolsó ismert jó" képviseli, így egyszerűen össze lehessen hasonlítani a be van jelölve a verzió és helyi kódját viselkedését. Ezután létrehozunk egy gyermek hely alapján ez a alapvető, így tudjuk tesztelheti a módosításokat *mywebapi* a nagyobb alkalmazás összefüggésében.

1. Klónozás a [fejlesztési tárolóhelyek mintaalkalmazás](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. A távoli ág kivétele *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Válassza ki a _fejlesztési_ terület: `azds space select --name dev`. Amikor a rendszer kéri, válassza ki a szülő fejlesztési szóközzel, válassza ki a  _\<nincs\>_.
1. Keresse meg a _mywebapi_ könyvtárat, és hajtsa végre: `azds up -d`
1. Keresse meg a _webfrontend_ könyvtárat, és hajtsa végre: `azds up -d`
1. Hajtsa végre `azds list-uris` megtekintheti a nyilvános végpontot _webfrontend_

> [!TIP]
> Az alapterv kézi beállítása a fenti lépéseket, de a CI/CD – automatikus naprakészen a referenciakonfiguráció véglegesített kód teams használatát javasoljuk.
>
> Tekintse meg a [útmutató az Azure DevOps CI/CD beállítása](../articles/dev-spaces/how-to/setup-cicd.md) munkafolyamat létrehozása a következő ábra hasonló.
>
> ![Példa a CI/CD-diagram](../articles/dev-spaces/media/common/ci-cd-complex.png)

Ezen a ponton a referenciakonfiguráció kell futtatnia. Futtassa az `azds list-up --all` parancsot. A következőhöz hasonló kimenet fog megjelenni:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A DevSpace oszlop jeleníti meg, hogy mindkét szolgáltatás fut-e nevű szóközzel _fejlesztési_. Bárki, aki megnyitja az URL-címe és a web app navigál meghívja a beadott kódot elérési útja, amely mindkét szolgáltatásokon keresztül. Most tegyük fel, hogy folytatja a fejlődő _mywebapi_. Hogyan módosíthatja a kódokat, és tesztelheti őket úgy, hogy nem akadályozza a fejlesztői környezetet használó többi fejlesztőt? Ehhez be kell állítania a saját terét.

### <a name="create-a-dev-space"></a>Dev Spaces-tér létrehozása
A saját verzióját futtatják _mywebapi_ eltérő szóközzel _fejlesztési_, a következő parancs segítségével hozhat létre saját terület:

```cmd
azds space select --name scott
```

Amikor a rendszer kéri, válassza ki a _fejlesztési_ , a **fejlesztési terület szülő**. Ez azt jelenti, hogy az új hely _dev/scott_, a hely lesz származtatva _fejlesztési_. Hamarosan kiderül, hogyan segít ez a tesztelésben.

A nevet használja tartja a bevezető cserélhető rendelkező, _scott_ társak azonosíthatja, ki dolgozik, így új terület. De lehet nevű semmit, és rugalmas kapcsolatos azt jelenti, például _sprint4_ vagy _bemutató_. Ismerős a helyzet, _fejlesztési_ dolgozik egy adott alkalmazás összes fejlesztők számára az alapkonfiguráció szolgál:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Futtassa az `azds space list` parancsot a fejlesztői környezetben lévő terek listájának megtekintéséhez. A _kijelölt_ oszlop azt jelzi, amely szóköz, jelenleg kiválasztott (true/false). Az Ön esetében a hely nevű _dev/scott_ létrehozásakor automatikusan kiválasztva. Az `azds space select` paranccsal bármikor kiválaszthat egy másik teret.

Lássuk működés közben.