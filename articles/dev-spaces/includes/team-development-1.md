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
ms.openlocfilehash: 404e238e51b7ac8b799f413965560a8d42ccc5df
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
Eddig meg már fut az alkalmazás kódjában, mintha a csak az alkalmazás dolgozó fejlesztők. Ebben a szakaszban megtudhatja, hogyan Azure fejlesztői szóközöket leegyszerűsíti a fejlesztési csapat:
* Fejlesztői célokat azonos fejlesztői környezetben csapata engedélyezése.
* Minden egyes a elkülönítési és mások számára megtörje való nélkül kódra léptetés fejlesztői támogatja.
* Kód-végpontok, kód véglegesítés előtt tesztelje mocks létrehozása vagy szimulálása függőségek nélkül.

## <a name="challenges-with-developing-microservices"></a>Mikroszolgáltatások fejlődő kihívásai
A mintaalkalmazás pillanatnyilag nem nagyon összetett. De a valós fejlesztési kihívást hamarosan merülnek fel, hozzáadhat további szolgáltatásokat, és a fejlesztői csapat növekszik.

Kép magát egy szolgáltatás, amely együttműködik az egyéb szolgáltatások több tucatnyi dolgozik.

- Mindent helyi fejlesztési futtatásához irreális válhat. A fejlesztői gépen nincs elegendő erőforrás a teljes alkalmazás. Vagy lehet, hogy az alkalmazás rendelkezik, csak a nyilvánosan elérhető végpontot (például az alkalmazás válaszol-e a webhook egy SaaS-alkalmazás).

- Csak az Ön e szolgáltatástól függő szolgáltatások futtatásához próbálkozhat, de ez azt jelenti, hogy meg kell tudhatja, hogy a teljes megszüntetésre függőségi (például a függőségek függőségek). Vagy nem könnyen ismerete, hogyan állíthatja össze és a függőségek futtatható, mivel azok nem dolgozik kérdése.
- Néhány fejlesztők szimulálva, vagy fel, a szolgáltatásfüggőségek számos mocking igénybe. Ez a megközelítés néha segítséget, de ezek mocks kezelése hamarosan átvehetik a saját fejlesztési elérhető. És ezt a megközelítést vezet a fejlesztői környezet keresése nagyon különböző üzemi környezetben, és finom hibák is kötés.
- Ez azt jelenti, hogy nehéz bármilyen típusú végpontok-tesztelés során válik. Integráció tesztelése csak reálisan fordulhat elő, utáni véglegesítési, ami azt jelenti, hogy látható problémákat később a fejlesztési ciklus.

![](../media/common/microservices-challenges.png)


## <a name="work-in-a-shared-development-environment"></a>Megosztott fejlesztői környezetben működik
Azure fejlesztői szóközök, beállíthat egy *megosztott* fejlesztési környezetet az Azure-ban. Minden egyes fejlesztői összpontosíthatnak, az alkalmazás a része, és ismételt fejleszthet *előre véglegesítése a határidő kód* olyan környezetben, amely már tartalmazza az összes egyéb szolgáltatások és felhőbeli erőforrásokat, amelyek a forgatókönyvek függenek. Függőségek mindig naprakészek legyenek, és a fejlesztők úgy, hogy az üzemi tükrözi dolgozik.

## <a name="work-in-your-own-space"></a>Munka a saját munkaterületen
Most kialakított kód a szolgáltatás, és készen áll, hogy a, mielőtt kód gyakran nem lehet jó állapotban. Meg van még mindig ismételt kialakításában, tesztelés és kísérletezés megoldásokkal. Azure fejlesztői szóközöket biztosítja a **terület**, amely lehetővé teszi, hogy munkahelyi elkülönítve, és a csapattagok számára megtörje való nélkül.

> [!Note]
> Mielőtt a folytatáshoz, zárjon be minden Visual STUDIO Code mindkét szolgáltatás esetében, és futtassa `azds up -d` minden, a szolgáltatás legfelső szintű mappák. (Ez az előzetes kizárólagosan.)

Megtudhatja, hogy ha a futó szolgáltatások részletes bemutatása. Futtassa a `azds list` parancsot, és megjelenik a kimenet az alábbihoz hasonló:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     http://webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

A terület az oszlopban látható, hogy mindkét szolgáltatás fut-e a neve szóközt `default`. Bárki, aki az URL-címe megnyílik, és a web app navigál meghívják a korábban megírt kód elérési, amelyen keresztül mindkét szolgáltatás. Most tegyük fel, hogy folytatja a fejlesztés `mywebapi`. Hogyan lehet kód módosításokat és tesztelését és nem szakítják meg a fejlesztői környezet használó más fejlesztők? Ehhez a saját területet kell beállítani.

## <a name="create-a-space"></a>Hozzon létre egy tárolóhelyet
A saját verziójának futtatásához `mywebapi` nem adható a `default`, létrehozhat saját lemezterület a következő paranccsal:

``` 
azds space create --name scott
```

A fenti példában, hogy az azonosítható a társaknak, hogy ez a terület a munka eddig használt a név az új tárhely, de hívása azt semmit, például és rugalmas arról, hogy mit jelent, mint a "sprint4" vagy "bemutató."

Futtassa a `azds space list` parancsot a fejlesztői környezetben csak szóközökből listájának megtekintéséhez. Egy csillag (*) jelenik meg a jelenleg kijelölt terület mellett. Abban az esetben a "scott" nevű hely automatikusan kiválasztott hozták létre. Választhat egy másik helyet a bármikor a `azds space select` parancsot.
