---
title: Útmutató a hivatkozási felvágási dokumentáció használatához – Azure digitális Twins | Microsoft Docs
description: Az Azure Digital Twins hencegő dokumentációjának használatának ismertetése.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023293"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure digitális ikrek – dokumentáció

Minden egyes kiépített Azure-beli digitális Twins-példány magában foglalja a saját automatikusan generált hencegő dokumentációját.

A [hencegés](https://swagger.io/)vagy a [OpenAPI](https://www.openapis.org/)összetett API-információkat egyesít egy interaktív és nyelv – agnosztikus hivatkozási erőforrásba. A hencegés kritikus fontosságú segédanyagot biztosít a JSON-adattartalom, a HTTP-metódusok és az API-k elleni műveletek végrehajtásához használható végpontok számára.

## <a name="swagger-summary"></a>Hencegés összegzése

A hencegés interaktív összefoglalást biztosít az API-ról, amely a következőket tartalmazza:

* API-és objektummodell-információk.
* REST API végpontok, amelyek meghatározzák a szükséges kérelmek hasznos adatait, fejléceit, paramétereit, környezeti elérési útját és HTTP-metódusait.
* Az API-funkciók tesztelése.
* Példa a HTTP-válaszok érvényesítésére és megerősítésére szolgáló válaszokra.
* Hibakód adatai.

A hencegés egy kényelmes eszköz, amely segítséget nyújt az Azure digitális Twins felügyeleti API-k fejlesztéséhez és teszteléséhez.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Leírások

Az automatikusan generált hencegő anyagminta gyors áttekintést nyújt a fontos fogalmakról, az elérhető felügyeleti API-végpontokról, valamint az egyes objektummodell-modellekről a fejlesztés és a tesztelés elősegítése érdekében.

A tömör összefoglalás leírja az API-t.

[![hencegés összegzése és API-áttekintési információk](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

A felügyeleti API-objektumok modelljei is fel vannak sorolva.

[a hencegő felhasználói felület alján felsorolt fel![i modellek](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

A főbb attribútumok részletesebb összefoglalásához kiválaszthatja az egyes felsorolt objektummodell-modelleket.

[a ![a modellek tartalmának olvasására kibontott hencegő modelleket](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

A létrehozott hencegő objektummodell az összes elérhető Azure digitális Twins [-objektum és API-](./concepts-objectmodel-spatialgraph.md)k olvasására alkalmas. A fejlesztők használhatják ezt az erőforrást, amikor az Azure Digital Twins megoldásait építenek.

## <a name="endpoint-summary"></a>Végpont összefoglalása

A hencegés emellett részletes áttekintést nyújt a felügyeleti API-kat alkotó összes végpontról.

A felsorolt végpontok a kért információkat is tartalmazzák, például a következőket:

* Kötelező paraméterek.
* A kötelező paraméter adattípusai.
* Az erőforrás eléréséhez használt HTTP-metódus.

[a hencegő felhasználói felületen megjelenő ![hencegő végpontok](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Válassza ki az egyes erőforrásokat a további tartalmak megjelenítéséhez, hogy részletesebb áttekintést kapjon.

## <a name="use-swagger-to-test-endpoints"></a>A végpontok tesztelése a hencegés használatával

Az egyik hatékony funkció, amely lehetővé teszi az API-végpontok közvetlen tesztelését a dokumentációs felhasználói felületen.

Miután kiválasztott egy végpontot, megjelenik a **kipróbálási** gomb.

[![hencegés kipróbálása gomb](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Bontsa ki a szakaszt, hogy minden szükséges és választható paraméterhez bevigye a beviteli mezőket. Adja meg a helyes értékeket, majd kattintson a **végrehajtás**gombra.

[![kipróbálni az eredmény példáját](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

A teszt végrehajtása után ellenőrizheti a válaszokat.

## <a name="swagger-response-data"></a>Hencegő válaszok adatvédelme

A felsorolt végpontok a válasz törzsének adatait is tartalmazzák a fejlesztés és a tesztek ellenőrzéséhez. Ilyenek például a sikeres HTTP-kérések állapotkódok és JSON-kódja.

[Példa ![hencegő JSON-válaszra](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

A példák olyan hibakódokat is tartalmaznak, amelyek segítenek a hibakeresésben vagy a sikertelen tesztek tökéletesítésében.

## <a name="swagger-oauth-20-authorization"></a>Hencegő OAuth 2,0-engedélyezés

> [!NOTE]
> * Az Azure Digital Twins-erőforrást létrehozó felhasználói fióknak van egy hely-rendszergazdai szerepkör-hozzárendelése, és további szerepkör-hozzárendeléseket is létrehozhat más felhasználók számára. A felhasználók és a hozzájuk tartozó szerepkörök engedélyt kaphatnak az API-k meghívására.

1. [A rövid](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) útmutató lépéseit követve hozzon létre és konfiguráljon egy Azure Active Directory alkalmazást. Azt is megteheti, hogy újból felhasznál egy meglévő alkalmazás-regisztrációt.

1. Adja hozzá a következő **átirányítási URI** -t a Azure Active Directory alkalmazás regisztrálásához:

    [a HRE-ben lévő hencegő átirányítási URL-cím ![](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name (Név)  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | A felügyeleti REST API dokumentációjának URL-címe megtalálható a portálon  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Jelölje be az **implicit engedélyezési** > **hozzáférési tokenek** jelölőnégyzetet a OAuth 2,0 implicit engedélyezési folyamat használatának engedélyezéséhez. Válassza a **Konfigurálás**, majd a **Mentés**lehetőséget.

1. Másolja a Azure Active Directory-alkalmazás **ügyfél-azonosítóját** .

A Azure Active Directory regisztrációjának befejezése után:

1. Kattintson az **Engedélyezés** gombra a hencegő oldalon.

    [![válassza a hencegés engedélyezése gombot](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Illessze be az alkalmazás AZONOSÍTÓját a **client_id** mezőbe.

    [![hencegés client_id mező](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Ezt követően a rendszer átirányítja a következő sikeres modális állapotba.

    [![a hencegő átirányítási modális](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Ha többet szeretne megtudni a OAuth 2,0 által védett interaktív tesztelési kérésekről, olvassa el a [hivatalos dokumentációt](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni az Azure Digital Twins Object models és a térbeli intelligencia gráfról, olvassa el a következőt: az [Azure digitális Twins Object models ismertetése](./concepts-objectmodel-spatialgraph.md).

- Ha szeretné megtudni, hogyan végezhet hitelesítést a felügyeleti API-val, olvassa el [a hitelesítést API](./security-authenticating-apis.md)-kkal című témakört.
