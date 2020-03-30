---
title: A swagger-dokumentáció használata - Azure Digital Twins | Microsoft dokumentumok
description: Az Azure Digital Twins Swagger referenciadokumentációhasználatának ismertetése.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023293"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Az Azure Digital Twins Swagger referenciadokumentációja

Minden kiépített Azure Digital Twins-példány saját, automatikusan létrehozott Swagger referenciadokumentációt tartalmaz.

[A Swagger](https://swagger.io/)vagy [az OpenAPI](https://www.openapis.org/)egyesíti az összetett API-információkat egy interaktív és nyelvfüggetlen referencia-erőforrássá. A Swagger kritikus referenciaanyagot biztosít, amelyről a JSON-adatok, a HTTP-metódusok és az API-n alapuló műveletek végrehajtásához használható konkrét végpontok.

## <a name="swagger-summary"></a>Swagger összefoglaló

A Swagger interaktív összefoglalót biztosít az API-ról, amely a következőket tartalmazza:

* API- és objektummodell-információk.
* REST API-végpontok, amelyek meghatározzák a szükséges kérelem hasznos terhelések, fejlécek, paraméterek, környezeti elérési utak és HTTP-metódusok.
* API-funkciók tesztelése.
* Példa a HTTP-válaszok ellenőrzésére és megerősítésére használt válaszadatokra.
* Hibakód-információ.

A Swagger egy kényelmes eszköz az Azure Digital Twins Management API-khoz intézett hívások fejlesztéséhez és teszteléséhez.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referenciaanyag

Az automatikusan létrehozott Swagger referenciaanyag gyors áttekintést nyújt a fontos fogalmakról, a rendelkezésre álló Felügyeleti API-végpontokról, valamint az egyes objektummodellek leírásáról a fejlesztés és tesztelés elősegítésére.

A tömör összefoglaló leírja az API-t.

[![Swagger összefoglaló és API áttekintésinformáció](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Felügyeleti API-objektum modellek is szerepelnek.

[![Swagger modellek szerepel alján Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

A kulcsattribútumok részletesebb összegzéséhez kiválaszthatja az egyes felsorolt objektummodelleket.

[![Swagger modellek bővült olvasni a modellek tartalmát](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

A létrehozott Swagger objektummodellek kényelmesen elolvashatják az összes elérhető Azure Digital Twins [objektumot és API-t.](./concepts-objectmodel-spatialgraph.md) A fejlesztők használhatják ezt az erőforrást, amikor megoldásokat hoznak létre az Azure Digital Twins-en.

## <a name="endpoint-summary"></a>Végpont összegzése

A Swagger a felügyeleti API-kat alkotó összes végpont alapos áttekintését is biztosítja.

Minden felsorolt végpont tartalmazza a szükséges kérési adatokat is, például a következőket:

* Kötelező paraméterek.
* Szükséges paraméteradattípusok.
* HTTP-módszer az erőforrás eléréséhez.

[![Swagger végpontok jelennek meg swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Válassza ki az egyes erőforrásokat a további tartalmuk megjelenítéséhez, hogy részletesebb áttekintést kapjon.

## <a name="use-swagger-to-test-endpoints"></a>Végpontok tesztelése swagger használatával

A Swagger által biztosított hatékony funkciók egyike az API-végpontok közvetlen tesztelésének lehetősége a dokumentációs felhasználói felületen keresztül.

Miután kiválasztott egy adott végpontot, megjelenik a **Kipróbálás** gomb.

[![Swagger Próbálja ki gomb](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Bontsa ki ezt a szakaszt, hogy az egyes szükséges és választható paraméterek bemeneti mezői jelenjenek meg. Adja meg a megfelelő értékeket, és válassza **a Végrehajtás lehetőséget.**

[![Swagger Próbálja ki eredmény példa](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

A teszt végrehajtása után ellenőrizheti a válaszadatokat.

## <a name="swagger-response-data"></a>Swagger válaszadatok

Minden felsorolt végpont is tartalmaz választörzs adatait a fejlesztés és a tesztek érvényesítéséhez. Ezek a példák közé tartoznak az állapotkódok és a JSON a sikeres HTTP-kérelmek.

[![Swagger JSON válasz példa](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

A példák hibakódokat is tartalmaznak a hibakeresés hez vagy a sikertelen tesztek javításához.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 engedélyezés

> [!NOTE]
> * Az Azure Digital Twins erőforrást létrehozó felhasználó egyszerű felhasználó rendelkezik egy tér-rendszergazdai szerepkör-hozzárendeléssel, és további szerepkör-hozzárendeléseket hozhat létre más felhasználók számára. Ezek a felhasználók és szerepköreik engedélyezhetők az API-k hívására.

1. Kövesse a [gyorsútmutató](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) lépéseit egy Azure Active Directory-alkalmazás létrehozásához és konfigurálásához. Azt is megteheti, hogy újra felhasználja a meglévő alkalmazásregisztrációt.

1. Adja hozzá a következő **átirányítási URI-t** az Azure Active Directory alkalmazás regisztrációjához:

    [![Swagger átirányítási url regisztrálása az AAD-ben](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Név  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | A felügyeleti REST API dokumentációs URL-címe a portálon található  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Jelölje be az **Implicit grant** > **Access tokenek jelölőnégyzetet,** ha engedélyezni szeretné az OAuth 2.0 implicit támogatási folyamatot. Válassza **a Konfigurálás**lehetőséget, majd **a Mentés lehetőséget.**

1. Másolja az Azure Active Directory-alkalmazás **ügyfélazonosítóját.**

Az Azure Active Directory regisztrációjának befejezése után:

1. Válassza az **Engedélyezés** gombot a swagger oldalon.

    [![A Swagger engedélyezés gombjának kiválasztása](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Illessze be az alkalmazásazonosítót a **client_id** mezőbe.

    [![Swagger client_id mező](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Ezután átirányítjuk a következő siker modális.

    [![Swagger átirányítás modális](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Ha többet szeretne megtudni az OAuth 2.0 által védett interaktív tesztelési kérelmekről, olvassa el a [hivatalos dokumentációt.](https://swagger.io/docs/specification/authentication/oauth2/)

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Digital Twins objektummodellekről és a térbeli intelligencia grafikonról, olvassa el [az Azure Digital Twins objektummodellek megértése](./concepts-objectmodel-spatialgraph.md)című olvasni.

- Ha meg szeretné tudni, hogyan hitelesítheti magát a Felügyeleti API-val, olvassa el a [Hitelesítés API-kkal (Hitelesítés API-kkal) ( Hitelesítés API-kkal](./security-authenticating-apis.md)) .
