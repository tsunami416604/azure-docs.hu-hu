---
title: Az Azure digitális Twins Swagger használatának megértése |} A Microsoft Docs
description: Az Azure digitális Twins Swagger használatával
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324244"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Az Azure digitális Twins Swagger használatával

Minden üzembe helyezett Azure digitális Twins-példány saját automatikusan generált Swagger dokumentációja tartalmazza.

[Swagger](https://swagger.io/) (vagy [OpenAPI](https://www.openapis.org/)) több egység egy interaktív és nyelvtől referencia erőforrás összetett API információval. Pontosabban a Swagger biztosít a kritikus fontosságú – referenciaanyag melyik JSON hasznos adatot, a HTTP-metódusok és a egy API-hoz műveletek végrehajtása az eszközspecifikus végpontokat.

## <a name="swagger-summary"></a>Swagger-összefoglaló

A swagger az API-t, beleértve egy interaktív összegzést tartalmaz:

* API és Hálózatiobjektum-modellt információkat.
* REST API-végpontjainak megadása szükséges a kérelem hasznos adat található, fejlécek, paraméterek, helyi elérési utak és HTTP-metódusok.
* API-funkciók teszteléséhez.
* Példa válasz adatok érvényesítése és a HTTP-válaszok megerősítésével.
* Hiba adatok.

Swagger eszköz, amely ezért egy kényelmes, amelyek segítik a fejlesztési és tesztelési, a felügyeleti API-hívások.

> [!TIP]
> Referenciaként a Swagger szakmai bemutatóhoz biztosítunk az API-funkció bemutatásához beállítása.
> Vannak tárolva [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

A saját, generált, felügyeleti API Swagger-dokumentációját a érhető el:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| `yourInstanceName` | Az Azure digitális Twins-példány nevét |
| `yourLocation` | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

## <a name="reference-material"></a>Leírások

Referenciaanyag az automatikusan létrehozott ismerteti a fontos fogalmakat és objektummodellt.

Rövid összefoglaló az API-t ismerteti:

![Swagger felső][1]

Core API objektummodellt is láthatók:

![Swagger-modellek][2]

Kattintson az egyes felsorolt Hálózatiobjektum-modellt fő jellemzőinek részletesebb összegzése:

![Swagger-modell][3]

A generált Swagger objektummodellt kényelmesek, tekintse meg az összes elérhető az Azure digitális Twins [objektumok és az API-k](./concepts-objectmodel-spatialgraph.md). Egy nagyszerű forrás, a fejlesztők számára, hogy az Azure digitális Twins megoldások létrehozásához.

## <a name="endpoint-summary"></a>A végpont összegzése

Swagger is minden olyan végpontok, az API-t alkotó alapos áttekintést nyújt.

Minden felsorolt végpont is tartalmazza a szükséges alkalmazáskérelem adatai például:

* Szükséges paraméterek.
* Kötelező paraméter adattípusokat.
* A HTTP-metódus az erőforrás eléréséhez.

![Swagger-végpont][4]

Az egyes erőforrások rákattintva további információkat talál.

## <a name="using-swagger-to-test-endpoints"></a>A Swagger használatával végpontok tesztelése

A Swagger biztosít hatékony funkciók egyik teszi **próbálja ki** vagy egy API-végpont közvetlenül a dokumentáció a felhasználói felület teszteléséhez.

Egy adott végpontba gombra kattintva megjelenik egy **kipróbálás** gombra:

![Próbálja meg swagger][5]

Minden szükséges és választható paraméter beviteli mezőket kimenetei bővítése az adott szakaszt. Ennek megfelelően adja meg az értékeket, és kattintson a **Execute**:

![Kísérlet történt a swagger][6]

A teszt végrehajtása után ellenőrizheti a érkezett válasz adatait.

## <a name="swagger-response-data"></a>Swagger érkezett válasz adatait

Minden felsorolt végpont válasz törzsében adatok ellenőrzése a fejlesztési és tesztek is tartalmaz. Ezek közé tartoznak a kívánt állapot- és JSON a sikeres HTTP-kéréseket.

![Swagger-válasz][7]

A példák is hibakódok javításához vagy javítása sikertelen tesztek segítségével.

## <a name="swagger-oauth-20-authorization"></a>OAuth 2.0 engedélyezési swagger

Interaktív módon teszteléséhez kérelmek ellen védi az OAuth 2.0 API-erőforrásokhoz, tekintse meg a [dokumentációs](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>További lépések

További információért olvassa el az Azure digitális Twins objektummodellt és a térbeli intelligencia graph [Ez a cikk](./concepts-objectmodel-spatialgraph.md).

Ismerje meg, hogyan hitelesítheti a felügyeleti API-val, olvassa el [API-kkal való hitelesítés közben](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
