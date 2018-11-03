---
title: Az Azure digitális Twins Swagger használatának megismerése |} A Microsoft Docs
description: Az Azure digitális Twins generáljon a swaggerrel
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 69b8f64574427a6b3d3d2cf0312eac88d86e5907
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960198"
---
# <a name="use-azure-digital-twins-swagger"></a>Az Azure digitális Twins generáljon a swaggerrel

Minden üzembe helyezett Azure digitális Twins-példány saját automatikusan generált Swagger dokumentációja tartalmazza.

[Swagger](https://swagger.io/), vagy [OpenAPI](https://www.openapis.org/), több egység egy interaktív és nyelvtől referencia erőforrás összetett API információval. Swagger biztosít a kritikus fontosságú – referenciaanyag melyik JSON hasznos adatot, a HTTP-metódusok és a egy API-hoz műveletek végrehajtása az eszközspecifikus végpontokat.

## <a name="swagger-summary"></a>Swagger-összefoglaló

Swagger az API-t, amely tartalmaz egy interaktív összegzést tartalmaz:

* API-t és az objektum modelladatokat.
* Adja meg a szükséges kérelem is észleltünk adattartalmakat, fejlécek, paraméterek, helyi elérési utak és HTTP-metódusok REST API-végpontokon.
* API-funkciók teszteléséhez.
* Példa a válasz információk érvényesíti, és erősítse meg a HTTP-válaszok.
* Hiba adatok.

A swagger, amelyek segítik a fejlesztési és tesztelési, a felügyeleti API-hívások eszköz.

> [!TIP]
> A Swagger szakmai bemutatóhoz biztosítunk az API-funkció bemutatásához beállítása.
> Vannak tárolva [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

A saját létrehozott felügyeleti API Swagger-dokumentációját a érhető el:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Egyéni attribútum neve | Csere erre |
| --- | --- |
| *Példánynév* | Az Azure digitális Twins-példány nevét |
| *yourLocation* | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

## <a name="reference-material"></a>Leírások

Referenciaanyag az automatikusan létrehozott ismerteti a fontos fogalmakat és objektummodellt.

Rövid összefoglaló ismerteti az API-t.

![Swagger felső][1]

Core API objektummodellt is megtalálható.

![Swagger-modellek][2]

Kiválaszthatja, hogy minden egyes listázott hálózatiobjektum-modellt fő jellemzőinek részletesebb összegzését.

![Swagger-modell][3]

A generált Swagger objektummodellt kényelmesek, tekintse meg az összes elérhető az Azure digitális Twins [objektumok és az API-k](./concepts-objectmodel-spatialgraph.md). A fejlesztők győződjön meg arról is, ha ezek a megoldások létrehozását az Azure digitális Twins az erőforrás használja.

## <a name="endpoint-summary"></a>A végpont összegzése

Swagger is minden olyan végpontok, az API-t alkotó alapos áttekintést nyújt.

Minden felsorolt végpont adatokat is tartalmaz a szükséges kérelem, mint például a:

* Szükséges paraméterek.
* Kötelező paraméter adattípusokat.
* HTTP-metódus az erőforrás eléréséhez.

![Swagger-végpont][4]

Részletesebb ismertetőt, jelölje ki az egyes erőforrások.

## <a name="use-swagger-to-test-endpoints"></a>A Swagger használatával végpontok tesztelése

A Swagger biztosít hatékony funkciók egyik lehetővé teszi a dokumentáció a felhasználói felületen keresztül közvetlenül egy API-végpont tesztelése.

Miután egy adott végpontnak választja, megjelenik **kipróbálás**.

![Próbálja meg swagger][5]

Bontsa ki a szakaszt viszi, megjelenik minden szükséges és választható paraméter beviteli mezőket. Ennek megfelelően adja meg az értékeket, és válassza ki **Execute**.

![Kísérlet történt a swagger][6]

A teszt végrehajtása, után ellenőrizheti a érkezett válasz adatait.

## <a name="swagger-response-data"></a>Swagger érkezett válasz adatait

Minden felsorolt végpont válasz törzsében adatok ellenőrzése a fejlesztési és tesztek is tartalmaz. Ezek közé tartoznak az állapot- és JSON meg szeretné tekinteni a sikeres HTTP-kéréseket.

![Swagger-válasz][7]

A példák is hibakódok javításához vagy javítása sikertelen tesztek segítségével.

## <a name="swagger-oauth-20-authorization"></a>OAuth 2.0 engedélyezési swagger

Interaktív módon teszteléséhez kérelmek ellen védi az OAuth 2.0 API-erőforrásokhoz, tekintse meg a [dokumentációs](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>További lépések

További információ az Azure digitális Twins objektum modellek és a térbeli intelligencia graph, olvassa el a [Azure digitális Ikerállapotának megismerése objektum modellek](./concepts-objectmodel-spatialgraph.md).

Ismerje meg, hogyan hitelesítheti a felügyeleti API-val, olvassa el [hitelesítés API-kkal](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
