---
title: Az Azure digitális Twins referencia a Swagger használatának megismerése |} A Microsoft Docs
description: Segédanyagok az Azure digitális Twins Swagger használatának megértéséhez.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/29/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 0b8c2b50e00c8e9727b09a454504d214a3060fe4
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502694"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Az Azure digitális Twins Swagger dokumentációja

Minden üzembe helyezett Azure digitális Twins-példány saját automatikusan generált Swagger dokumentációja tartalmazza.

[Swagger](https://swagger.io/), vagy [OpenAPI](https://www.openapis.org/), több egység egy interaktív és nyelvtől referencia erőforrás összetett API információval. Swagger biztosít a kritikus fontosságú – referenciaanyag melyik JSON hasznos adatot, a HTTP-metódusok és a egy API-hoz műveletek végrehajtása az eszközspecifikus végpontokat.

## <a name="swagger-summary"></a>Swagger-összefoglaló

Swagger az API-t, amely tartalmaz egy interaktív összegzést tartalmaz:

* API-t és az objektum modelladatokat.
* Adja meg a szükséges kérelem is észleltünk adattartalmakat, fejlécek, paraméterek, helyi elérési utak és HTTP-metódusok REST API-végpontokon.
* API-funkciók teszteléséhez.
* Példa a válasz információk érvényesíti, és erősítse meg a HTTP-válaszok.
* Hiba adatok.

A swagger, amelyek segítik a fejlesztési és tesztelési, az Azure digitális Twins felügyeleti API-hívások eszköz.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Leírások

Az automatikusan generált Swagger-referenciaanyag választékát kínálja a fontos fogalmakat, elérhető felügyeleti API-végpont és az egyes hálózatiobjektum-modellje, amelyek segítik a fejlesztési és tesztelési leírását gyors áttekintést.

Rövid összefoglaló ismerteti az API-t.

[![Swagger felső](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Felügyeleti API-objektum modellek is megtalálható.

[![Swagger-modellek](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

Kiválaszthatja, hogy minden egyes listázott hálózatiobjektum-modellt fő jellemzőinek részletesebb összegzését.

[![Swagger-modell](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

A generált Swagger objektummodellt kényelmesek, tekintse meg az összes elérhető az Azure digitális Twins [objektumok és az API-k](./concepts-objectmodel-spatialgraph.md). A fejlesztők használhatják ehhez az erőforráshoz, amikor ezek a megoldások létrehozását az Azure digitális Twins.

## <a name="endpoint-summary"></a>A végpont összegzése

Swagger is a felügyeleti API-k alkotó összes végpont alapos áttekintést nyújt.

Minden felsorolt végpont adatokat is tartalmaz a szükséges kérelem, mint például a:

* Szükséges paraméterek.
* Kötelező paraméter adattípusokat.
* HTTP-metódus az erőforrás eléréséhez.

[![Swagger-végpont](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

Részletesebb ismertetőt, jelölje ki az egyes erőforrások.

## <a name="use-swagger-to-test-endpoints"></a>A Swagger használatával végpontok tesztelése

A Swagger biztosít hatékony funkciók egyik lehetővé teszi a dokumentáció a felhasználói felületen keresztül közvetlenül egy API-végpont tesztelése.

Miután egy adott végpontnak választja, megjelenik **kipróbálás**.

[![Próbálja meg swagger](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

Bontsa ki a szakaszt viszi, megjelenik minden szükséges és választható paraméter beviteli mezőket. Adja meg a megfelelő értékeket, és válassza ki **Execute**.

[![Kísérlet történt a swagger](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

A teszt végrehajtása, után ellenőrizheti a érkezett válasz adatait.

## <a name="swagger-response-data"></a>Swagger érkezett válasz adatait

Minden felsorolt végpont válasz törzsében adatok ellenőrzése a fejlesztési és tesztek is tartalmaz. Ezek közé tartoznak az állapot- és JSON meg szeretné tekinteni a sikeres HTTP-kéréseket.

[![Swagger-válasz](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

A példák is hibakódok javításához vagy javítása sikertelen tesztek segítségével.

## <a name="swagger-oauth-20-authorization"></a>OAuth 2.0 engedélyezési swagger

> [!NOTE]
> * A résztvevő felhasználó, aki létrehozta az Azure digitális Twins erőforrás terület rendszergazdai szerepkör-hozzárendelés lesz, és tudni hozzon létre további szerepkör-hozzárendelések más felhasználók számára. Ezek a felhasználók és azok szerepköreivel jogosult-e az API-kat.

1. Kövesse a [ebben a rövid útmutatóban](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) vagy [az Azure digitális Twins alkalmazás regisztrálása az Azure Active Directory örökölt](./how-to-use-legacy-aad.md) létrehozása és konfigurálása az Azure AD-alkalmazást. Másik lehetőségként egy meglévő alkalmazásregisztráció felhasználhatja.

1. Vegye fel az alkalmazásregisztráció a következő válasz URL-cím:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name (Név)  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | A felügyeleti REST API dokumentációjában található URL-címe a portálon  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Másolja ki az Azure AD-alkalmazás azonosítója.

Elvégezte az Azure Active Directory eszközregisztrációs:

1. Válassza ki a **engedélyezés** gomb a swagger-lapon.

    [![Válassza ki a Swagger gomb engedélyezése](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Illessze be az Alkalmazásazonosítót a **client_id** mező.

    [![Swagger client_id mező](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Ezután a következő modális sikeres irányítja.

    [![Swagger-átirányítási modális paneljére](media/how-to-use-swagger/swagger_auth_redirect.png)](media/how-to-use-swagger/swagger_auth_redirect.png#lightbox)

Interaktív módon az OAuth 2.0 által védett kérelmek tesztelésével kapcsolatos további tudnivalókért tekintse meg a [dokumentációs](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>További lépések

- További információ az Azure digitális Twins objektum modellek és a térbeli intelligencia graph, olvassa el a [Azure digitális Ikerállapotának megismerése objektum modellek](./concepts-objectmodel-spatialgraph.md).

- Ismerje meg, hogyan hitelesítheti a felügyeleti API-val, olvassa el [hitelesítés API-kkal](./security-authenticating-apis.md).