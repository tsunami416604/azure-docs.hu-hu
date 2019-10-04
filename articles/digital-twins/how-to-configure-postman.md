---
title: A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz | Microsoft Docs
description: A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 14e6a52f86586eaae019d9658c2f813a15fc3474
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949208"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz

Ez a cikk bemutatja, hogyan konfigurálhatja a Poster REST-ügyfelet az Azure digitális Twins felügyeleti API-k használatához és teszteléséhez. Pontosabban leírja a következőket:

* Azure Active Directory alkalmazás konfigurálása a OAuth 2,0 implicit engedélyezési folyamat használatára.
* A Poster REST-ügyfél használata, hogy jogkivonat-tartalmú HTTP-kéréseket készítsen a felügyeleti API-khoz.
* A Poster használatával többrészes POST-kérelmeket készíthet a felügyeleti API-khoz.

## <a name="postman-summary"></a>Poster – összefoglalás

Ismerkedjen meg az Azure Digital Twins szolgáltatással egy REST-ügyfél eszközzel, például a [Poster](https://www.getpostman.com/) használatával, és készítse elő a helyi tesztelési környezetet. A Poster-ügyfél segít az összetett HTTP-kérések gyors létrehozásában. A Poster-ügyfél asztali verziójának letöltéséhez nyissa meg a [www.getpostman.com/apps](https://www.getpostman.com/apps).

A [Poster](https://www.getpostman.com/) egy Rest-tesztelési eszköz, amely a kulcsfontosságú HTTP-kérések funkcióit egy hasznos asztali és beépülő modul-alapú grafikus felhasználói felületre helyezi.

A Poster-ügyfélen keresztül a megoldások fejlesztői megadhatják a HTTP-kérelem típusát (*post*, *Get*, *Update*, *patch*és *delete*), az API-végpontot a híváshoz és az SSL használatát. A Poster a HTTP-kérelmek fejlécének, paramétereinek, űrlap-és testületeinek hozzáadását is támogatja.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Azure Active Directory konfigurálása a OAuth 2,0 implicit engedélyezési folyamat használatára

Konfigurálja a Azure Active Directory alkalmazást az OAuth 2,0 implicit engedélyezési folyamat használatára.

1. Nyissa meg az **API-engedélyek** panelt az alkalmazás regisztrálásához. Válassza **az engedély hozzáadása** gombot. A **kérelem API-engedélyek** ablaktáblán válassza a **saját szervezet által használt API** -k fület, majd keresse meg a következőt:
    
    1. `Azure Digital Twins`. Válassza ki az **Azure Digital Twins** API-t.

        [![Search API vagy Azure digitális Twins](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Vagy keressen rá a `Azure Smart Spaces Service` kifejezésre. Válassza ki az **Azure Smart Spaces szolgáltatás** API-ját.

        [@no__t – 1Search API az Azure intelligens tárhelyekhez](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > A megjelenő Azure AD API-név és-azonosító a bérlőtől függ:
    > * A bérlői és az ügyfél-fiókok teszteléséhez `Azure Digital Twins` értéket kell keresni.
    > * Más Microsoft-fiókoknak a `Azure Smart Spaces Service` kifejezésre kell keresniük.

1. A kiválasztott API az **Azure Digital Twins** néven jelenik meg ugyanabban a **kérelem API-engedélyek** ablaktáblán. Válassza az **olvasás (1)** legördülő listát, majd válassza az **olvasás. írás** jelölőnégyzetet. Kattintson az **engedélyek hozzáadása** gombra.

    [![API-engedélyek hozzáadása](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. A szervezet beállításaitól függően előfordulhat, hogy további lépéseket kell tennie ahhoz, hogy rendszergazdai hozzáférést biztosítson ehhez az API-hoz. További információért forduljon a rendszergazdához. A rendszergazdai hozzáférés jóváhagyása után az **API-engedélyek** ablaktáblán a **rendszergazdai jóváhagyás szükséges** oszlop az API-khoz hasonlóan fog megjelenni:

    [![API-engedélyek hozzáadása](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)


1. Válassza a **jegyzékfájl** lehetőséget az alkalmazáshoz tartozó alkalmazási jegyzékfájl megnyitásához. *Oauth2AllowImplicitFlow* beállítása a következőre: `true`.

    [@no__t – 1Azure Active Directory implicit folyamat](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Adja meg a **Válasz URL-címét** `https://www.getpostman.com/oauth2/callback` értékre.

    [@no__t – 1Azure Active Directory válasz URL-címe](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Másolja és őrizze meg Azure Active Directory **alkalmazásának azonosítóját** . Ezt az alábbi lépések használják.

   [@no__t – 1Azure Active Directory alkalmazás azonosítója](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>OAuth 2,0 token beszerzése

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

A Poster beállítása és konfigurálása Azure Active Directory token beszerzéséhez. Ezt követően hozzon végre egy hitelesített HTTP-kérést az Azure Digital Twins számára a beszerzett jogkivonat használatával:

1. Az alkalmazás letöltéséhez nyissa meg a [www.getpostman.com](https://www.getpostman.com/) .
1. Ellenőrizze, hogy helyes-e az **engedélyezési URL-cím** . A formátum a következőket teszi:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name (Név)  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | A bérlő vagy szervezet neve | `microsoft` |

1. Válassza az **Engedélyezés** lapot, válassza a **OAuth 2,0**, majd az **új hozzáférési jogkivonat beolvasása**elemet.

    | Mező  | Value |
    |---------|---------|
    | Engedélyezési típus | `Implicit` |
    | Visszahívási URL | `https://www.getpostman.com/oauth2/callback` |
    | Hitelesítési URL-cím | Az **engedélyezési URL-cím** használata a **2. lépésből** |
    | Ügyfél-azonosító | Az előző szakaszban létrehozott vagy újrafelhasznált Azure Active Directory **alkalmazás azonosítójának** használata |
    | Scope | Hagyja üresen |
    | State | Hagyja üresen |
    | Ügyfél-hitelesítés | `Send as Basic Auth header` |

1. Az ügyfélnek ekkor a következőképpen kell megjelennie:

    [@no__t – 1Postman-ügyfél – példa](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Válassza a **kérelem tokenje**elemet.

    >[!TIP]
    >Ha a "OAuth 2 nem sikerült befejezni" hibaüzenet jelenik meg, próbálja meg a következőket:
    > * Zárjuk be a Poster-t, majd nyissa meg újra, és próbálkozzon újra.
  
1. Görgessen le, és válassza a **token használata**lehetőséget.

## <a name="make-a-multipart-post-request"></a>Többrészes POST-kérelem létrehozása

Az előző lépések elvégzése után konfigurálja a Poster-t egy hitelesített HTTP többrészes POST-kérelem létrehozásához:

1. A **fejléc** lapon adjon hozzá egy HTTP-kérelem fejlécének kulcsához tartozó **Content-Type** értéket a `multipart/mixed` értékkel.

   [@no__t – 1Content típus többrészes/kevert](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Nem szöveges adatfájlok szerializálása fájlokba. A JSON-fájlok JSON-fájlként lesznek mentve.
1. A **törzs** lapon válassza a `form-data` elemet. 
1. Adja hozzá az egyes fájlokat egy **Kulcsnév** hozzárendelésével, majd válassza a `file` elemet.
1. Ezután válassza ki az egyes fájlokat a **Fájl választása** gombbal.

   [@no__t – 1Postman-ügyfél – példa](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * A Poster-ügyfélnek nincs szüksége arra, hogy a többrészes adattömbökhöz manuálisan hozzárendelt **tartalom-típus** vagy **tartalom-törlés**legyen.
   > * Az egyes részekhez nem kell megadnia ezeket a fejléceket.
   > * A teljes kérelemhez ki kell választania `multipart/mixed` vagy más megfelelő **Content-Type értéket** .

1. Végül válassza a **Küldés** lehetőséget a többrészes http post-kérelem elküldéséhez.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a digitális Twins felügyeleti API-król és azok használatáról, olvassa el az [Azure digitális Twins felügyeleti API](how-to-navigate-apis.md)-k használatát ismertető témakört.

- A többrészes kérelmek használatával [blobokat adhat hozzá az Azure Digital Twins entitásokhoz](./how-to-add-blobs.md).

- A felügyeleti API-k hitelesítésének megismeréséhez olvassa el a [hitelesítés API](./security-authenticating-apis.md)-kkal című témakört.
