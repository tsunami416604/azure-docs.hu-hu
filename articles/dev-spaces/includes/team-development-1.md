---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 0d3bdb6cc56b90d1975af73be1bb8cc1f73e1213
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129121"
---
Eddig úgy futtatta alkalmazása kódját, mintha csak Ön dolgozna fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Lehetővé teszi, hogy egy fejlesztőcsapat ugyanabban a környezetben dolgozhasson. Ez lehet egy megosztott Dev Spaces-tér, vagy igény szerint több különálló Dev Spaces-tér.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazása jelenleg nem túl összetett. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat.

Képzelje el, hogy egy olyan szolgáltatáson dolgozik, amely több tucat más szolgáltatással van kapcsolatban.

- Hamar életszerűtlenné válhat, hogy mindent helyileg futtasson a fejlesztéshez. Elképzelhető, hogy a fejlesztői gépen nincs elég erőforrás a teljes alkalmazás futtatásához. Az is lehetséges, hogy az alkalmazása olyan végpontokkal rendelkezik, amelyeknek nyilvánosan elérhetőnek kell lenniük (pl. az alkalmazása egy SaaS-alkalmazás webhookjának ad választ).

- Megpróbálhatja csak azokat a szolgáltatásokat futtatni, amelyektől függ az alkalmazás, de ehhez ismernie kell a függőségek teljes körét (például a függőségek függőségeit). Az is problémát jelenthet, hogy nehéznek bizonyul a függőségek felépítése és futtatása, mert korábban nem dolgozott rajtuk.
- Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez a módszer néha segíthet, de az utánzatok kezelése hamarosan önmagában is saját fejlesztői erőforrásokat köthet le. Ez a megközelítés ahhoz vezethet, hogy a Dev Spaces-tér nagyon sokban eltér az éles üzemtől, és apró hibák jelenhetnek meg.
- Ebből következik, hogy mindenféle végpontok közötti tesztelés nehézkessé válik. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

![](../media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

> [!Note]
> A folytatás előtt zárja be az összes VS Code-ablakot mindkét szolgáltatásban, majd futtassa az `azds up -d` parancsot a szolgáltatások gyökérmappájában. (Ez egy előzetes verziójú korlátozás.)

Tekintsük meg, hogy hol futnak jelenleg a szolgáltatások. Futtassa az `azds list-up` parancsot. A következőhöz hasonló kimenet fog megjelenni:

```
Name                          DevSpace  Type     Updated      Status
----------------------------  --------  -------  -----------  ----------------
mywebapi                      default   Service  10m 1s ago   Running
mywebapi-54f9cf5b59-bjnkm     default   Pod      10m 4s ago   Running
webfrontend-5b697958d6-b6v96  default   Pod      26m 38s ago  Init:1/3:mindaro-build
```

A DevSpace oszlop azt mutatja, hogy mindkét szolgáltatás a `default` nevű térben fut. Bárki, aki megnyitja a nyilvános URL-címet, és felkeresi a webalkalmazást, meghívja az Ön által korábban megírt kódútvonalat, amely mindkét szolgáltatást futtatja. Tegyük fel, hogy folytatni szeretné a `mywebapi` fejlesztését. Hogyan módosíthatja a kódokat, és tesztelheti őket úgy, hogy nem akadályozza a fejlesztői környezetet használó többi fejlesztőt? Ehhez be kell állítania a saját terét.

### <a name="create-a-dev-space"></a>Dev Spaces-tér létrehozása
Ha a `mywebapi` saját verzióját nem a `default` térben szeretné futtatni, létrehozhat egy saját teret a következő paranccsal:

``` 
azds space select --name scott
```

Amikor a rendszer kéri, válassza a `default` elemet a **szülő Dev Spaces-térként**. Ez azt jelenti, hogy az új tér, a `default/scott`, a `default` térből lesz származtatva. Hamarosan kiderül, hogyan segít ez a tesztelésben. 

A fenti példában a saját nevemet használtam az új térhez, így a munkatársaim is könnyen be tudják azonosítani, hogy ebben a térben dolgozom, de bárhogyan elnevezheti, és rugalmasan kezelheti a név jelentését (lehet például „sprint4” vagy „demo”).

Futtassa az `azds space list` parancsot a fejlesztői környezetben lévő terek listájának megtekintéséhez. A jelenleg kiválasztott tér mellett egy csillag (*) jelenik meg. Az Ön esetében a rendszer automatikusan kiválasztotta az alapértelmezett/scott nevű teret, amikor létrejött. Az `azds space select` paranccsal bármikor kiválaszthat egy másik teret.
