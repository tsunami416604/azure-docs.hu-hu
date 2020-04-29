---
title: A Poster konfigurálása – Azure digitális Twins | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és használhatja a Poster-t az Azure digitális Twins API-k teszteléséhez.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297157"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz

Ez a cikk bemutatja, hogyan konfigurálhatja a Poster REST-ügyfelet az Azure digitális Twins felügyeleti API-k használatához és teszteléséhez. Pontosabban leírja a következőket:

* Azure Active Directory alkalmazás konfigurálása a OAuth 2,0 implicit engedélyezési folyamat használatára.
* A Poster REST-ügyfél használata, hogy jogkivonat-tartalmú HTTP-kéréseket készítsen a felügyeleti API-khoz.
* A Poster használatával többrészes POST-kérelmeket készíthet a felügyeleti API-khoz.

## <a name="postman-summary"></a>Poster – összefoglalás

Ismerkedjen meg az Azure Digital Twins szolgáltatással egy REST-ügyfél eszközzel, például a [Poster](https://www.getpostman.com/) használatával, és készítse elő a helyi tesztelési környezetet. A Poster-ügyfél segít az összetett HTTP-kérések gyors létrehozásában. A Poster-ügyfél asztali verziójának letöltéséhez nyissa meg a [www.getpostman.com/apps](https://www.getpostman.com/apps).

A [Poster](https://www.getpostman.com/) egy Rest-tesztelési eszköz, amely a kulcsfontosságú HTTP-kérések funkcióit egy hasznos asztali és beépülő modul-alapú grafikus felhasználói felületre helyezi.

A Poster-ügyfélen keresztül a megoldások fejlesztői megadhatják a HTTP-kérések típusát (*post*, *Get*, *Update*, *patch*és *delete*), az API-végpontot, amellyel a TLS-t hívhatják és használhatják. A Poster a HTTP-kérelmek fejlécének, paramétereinek, űrlap-és testületeinek hozzáadását is támogatja.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Azure Active Directory konfigurálása a OAuth 2,0 implicit engedélyezési folyamat használatára

1. [A rövid](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) útmutató lépéseit követve hozzon létre és konfiguráljon egy Azure Active Directory alkalmazást. Azt is megteheti, hogy újból felhasznál egy meglévő alkalmazás-regisztrációt.

    [![Új Poster átirányítási URI konfigurálása](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Most adjon hozzá egy **átirányítási URI** -t a `https://www.getpostman.com/oauth2/callback`következőhöz:.

1. Jelölje be az **implicit** > **hozzáférési jogkivonatok** jelölőnégyzetet a OAuth 2,0 implicit engedélyezési folyamat használatának engedélyezéséhez. Válassza a **Konfigurálás**, majd a **Mentés**lehetőséget.

1. Másolja a Azure Active Directory-alkalmazás **ügyfél-azonosítóját** .

## <a name="obtain-an-oauth-20-token"></a>OAuth 2,0 token beszerzése

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

A Poster beállítása és konfigurálása Azure Active Directory token beszerzéséhez. Ezt követően hozzon végre egy hitelesített HTTP-kérést az Azure Digital Twins számára a beszerzett jogkivonat használatával:

1. Ellenőrizze, hogy helyes-e az **engedélyezési URL-cím** . A formátum a következőket teszi:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name (Név)  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | A bérlő vagy szervezet neve. A Azure Active Directory alkalmazás regisztrációjának alfanumerikus **bérlői azonosítója** helyett használja az emberi felhasználóbarát nevet. | `microsoft` |

1. Az alkalmazás letöltéséhez nyissa meg a [www.getpostman.com](https://www.getpostman.com/) .

1. A GET kérést szeretnénk tenni. Válassza az **Engedélyezés** lapot, válassza a OAuth 2,0, majd az **új hozzáférési jogkivonat beolvasása**elemet.

    | Mező  | Érték |
    |---------|---------|
    | Engedélyezési típus | `Implicit` |
    | Visszahívási URL | `https://www.getpostman.com/oauth2/callback` |
    | Hitelesítési URL-cím | Az **1. lépésben** szereplő **engedélyezési URL-cím** használata |
    | Ügyfél-azonosító | Az előző szakaszban létrehozott vagy újrafelhasznált Azure Active Directory **alkalmazás azonosítójának** használata |
    | Hatókör | Hagyja üresen |
    | Állapot | Hagyja üresen |
    | Ügyfél-hitelesítés | `Send as Basic Auth header` |

1. Az ügyfélnek ekkor a következőképpen kell megjelennie:

    [![Poster-ügyfél jogkivonat-példája](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Válassza a **Request Token** (Jogkivonat kérése) lehetőséget.
  
1. Görgessen le, és válassza a **token használata**lehetőséget.

## <a name="make-a-multipart-post-request"></a>Többrészes POST-kérelem létrehozása

Az előző lépések elvégzése után konfigurálja a Poster-t egy hitelesített HTTP többrészes POST-kérelem létrehozásához:

1. A **fejlécek** lapon adja hozzá a HTTP-kérelem fejlécének kulcsához tartozó **Content-Type** értéket `multipart/mixed`.

   [![Tartalom típusának megadása többrészes/vegyes](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Nem szöveges adatfájlok szerializálása fájlokba. A JSON-fájlok JSON-fájlként lesznek mentve.
1. A **törzs** lapon válassza a elemet `form-data`. 
1. Adja hozzá az egyes fájlokat egy **Kulcsnév** hozzárendelésével, majd `File`válassza a elemet.
1. Ezután válassza ki az egyes fájlokat a **Fájl választása** gombbal.

   [![Poster-ügyfél űrlapjának szövegtörzse – példa](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * A Poster-ügyfélnek nincs szüksége arra, hogy a többrészes adattömbökhöz manuálisan hozzárendelt **tartalom-típus** vagy **tartalom-törlés**legyen.
   > * Az egyes részekhez nem kell megadnia ezeket a fejléceket.
   > * Ki kell választania `multipart/mixed` vagy egy másik megfelelő **Content-Type értéket** a teljes kérelemhez.

1. Végül válassza a **Küldés** lehetőséget a többrészes http post-kérelem elküldéséhez. A `200` vagy `201` a sikeres kérést jelző állapotkód. A megfelelő válaszüzenet megjelenik az ügyfél felületén.

1. Ellenőrizze a HTTP POST-kérelmek adatait az API-végpont meghívásával: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a digitális Twins felügyeleti API-król és azok használatáról, olvassa el az [Azure digitális Twins felügyeleti API](how-to-navigate-apis.md)-k használatát ismertető témakört.

- A többrészes kérelmek használatával [blobokat adhat hozzá az Azure Digital Twins entitásokhoz](./how-to-add-blobs.md).

- A felügyeleti API-k hitelesítésének megismeréséhez olvassa el a [hitelesítés API](./security-authenticating-apis.md)-kkal című témakört.
